# Simulated Attack

## Sui Validator Exposure Analysis

### Executive Summary / TL;DR

The Sui Network is Vulnerable to Coordinated Attacks Leading to Network Halt.

Our external security scan found that validators controlling 3,955 of 10,000 voting power (39.6%) have critical vulnerabilities including open SSH ports and unpatched CVEs across multiple services. Since Sui halts at ≥3,334 voting power loss, **the network currently exceeds the failure threshold by 621 voting power** - a razor-thin margin.

**Key findings:**

- **39.6% of voting power** is externally exploitable via known vulnerabilities
- Network is **only 621 voting power away** from consensus failure
- Scanned **public services only** - internal security is likely worse
- Compromised validators could take **days to recover** (if they have backups at all)
- **2-4 week window** before active exploitation likely begins

**Attack timeline if exploited:**

- Hour 0: Automated exploitation of known CVEs begins
- Hour 1-6: Validators start going offline, keys stolen
- Hour 6-12: Network halts as 1/3+ voting power compromised
- Day 1-3: Frantic recovery attempts, many validators have no backups
- Day 3-7: Partial network recovery with reduced validator set
- Week 2+: Full recovery (if lucky)

**Bottom line:** Sui is one coordinated attack away from a multi-day network outage.

As part of our ongoing work at PGDN, we performed an **outside-in analysis** of the Sui validator set.

This document explains **exactly how the data was gathered, how we scored it, and how we quantified the risk to consensus.**

## Step 1: Collecting the Validator Set

We pulled the **active validator list** directly from a Sui mainnet RPC node:

```bash
curl -s https://fullnode.mainnet.sui.io:443   -H 'content-type: application/json'   -d '{"jsonrpc":"2.0","id":1,"method":"suix_getLatestSuiSystemState","params":[]}'

```

This returned the validator metadata for the current epoch, including:

- `suiAddress`
- `netAddress` / `p2pAddress` (network endpoints)
- `votingPower` (stake weight in the epoch)

We then exported this into our application for analysis.

## Step 2: Running Our Scanners

Each validator endpoint was probed using PGDN's agentic scanners.

For every IP/hostname discovered, we checked:

- **SSH exposure** (port 22 open)
- **Service banners & versions** across all open ports
- **CVE matches** against known vulnerable versions on ANY exposed service (not just SSH)
- **Other open ports** (RPC, metrics, dashboards, Docker API, etc.)

**Critical limitation:** We only scanned publicly accessible services and ports. Internal vulnerabilities, lateral movement opportunities, supply chain issues, and private network exposures remain unknown. The actual attack surface is likely significantly larger than what we've identified.

## Step 3: Assigning Risk Scores

Each validator was assigned a **risk score** based on exposures:

- **SSH open** → flagged as high-risk
- **Detected CVEs** → weighted by severity & count (found on multiple services including web servers)
- **Other misconfigs** (exposed Docker API on port 2375, etc.) → contributing factors

**Important note:** CVEs were detected across various exposed services, not limited to SSH. This includes vulnerable versions of Apache for example, and other common infrastructure components.

**About PGDN Scoring:** While the risk scores are relevant for this analysis, they serve a broader purpose in PGDN's ecosystem. We publish anonymized security scores to the blockchain (without specific CVE details or sensitive data) to create transparency around validator security posture. Validators can claim their nodes through a secure ownership verification process to access detailed findings and improve their security. This scoring system incentivizes better security practices across the entire network.

This produced a numeric risk score per validator.

## Step 4: Summing Stake Weight of Exposed Validators

While PGDN's scoring system serves broader security transparency goals, for this analysis we focused specifically on consensus risk.

Consensus risk in Sui is determined by **stake voting power**, not just validator count.

- Total voting power in each epoch: **10,000**
- Quorum for progress: **> 6,667**
- Liveness breaks if **≥ 3,334 voting power** goes offline/malicious

We cross-referenced our risk flags (`ssh_open` or `cves_count > 0`) with the validator set and summed their **votingPower**.

**Result:**

Validators with SSH open and/or CVEs represent **3,955 / 10,000 voting power (39.6%)**.

This calculation is independent of PGDN's public scoring system - it's a direct assessment of network liveness risk.

## Step 5: Interpretation

Because this total exceeds the **1/3 safety threshold (3,334)**, an attacker exploiting these exposures could plausibly disrupt consensus liveness.

This is not theoretical - the numbers show that current validator exposures **cross the line where a chain halt is possible.**

**The reality is likely worse:**

- This 39.6% figure only represents what's visible from the outside
- Validators running SSH on port 22 with CVEs are unlikely to have proper key management, backups, or internal security
- A coordinated attack could potentially wipe out validators entirely, not just take them offline temporarily
- Recovery times could be measured in days or weeks, not hours

## Attack Scenario Considerations

The identified vulnerabilities present multiple attack vectors:

- Direct exploitation via unpatched CVEs on various services
- SSH brute-force or credential stuffing attacks
- Potential container escape via Docker API exposure

**Critical Assumption:** Validators who fail to secure public-facing services likely have equally poor internal security. This is not speculation - it's a pattern observed across thousands of security incidents. Poor external hygiene correlates strongly with:

- No network segmentation
- Plaintext secrets in environment variables
- Unencrypted private keys on disk
- No backup strategies
- Shared root passwords
- Disabled audit logging

**Post-Compromise Reality - What Attackers Would Find:**

**Immediate Impact (Hours) (hypothetical):**

- Validator private keys (often stored unencrypted)
- Credentials in plain text
- Complete validator configuration including seed phrases
- Potential access to hot wallets and operational funds

**Destructive Scenarios (Days):**

- **Validator Deletion**: Wipe the validator node entirely - minimum 2-day recovery assuming backups exist
- **Double-signing Attack**: Use stolen keys to trigger slashing penalties
- **Consensus Manipulation**: Coordinate compromised validators to vote maliciously
- **Targeted Attack**: Focus on highest voting-power validators first - taking down just the top 20-30 vulnerable validators could halt the network

**Long-term Exploitation (Weeks/Months):**

- **Silent Key Theft**: Exfiltrate private keys and wait for optimal timing
- **Persistent Backdoors**: Install rootkits for long-term access
- **Supply Chain Poisoning**: Modify validator software updates
- **Funds Drainage**: Slowly siphon staking rewards to avoid detection
- **Intelligence Gathering**: Map the entire validator ecosystem for future attacks

The true attack surface is almost certainly larger than what we've documented.

## Coordinated Attack Model on Sui

A coordinated attack has to treat the **network as a single entity**, not just individual nodes. By scanning the validator set holistically, we found that ~40% of total voting power shares the **same exposed services and repeated CVEs**.  

### Phase 1 Reconnaissance
- Attacker scans the validator set.  
- Identifies ~40% of voting power with exploitable vulnerabilities.  
- Maps which validators have the highest voting weight.

### Phase 2 Preparation (hours to days)
- Stage infrastructure to target multiple validators simultaneously.  
- Prepare exploits for the specific CVEs.  
- Could leverage cloud infrastructure or a rented botnet.  

### Phase 3 Execution (minutes to hours)
**Option A – Quick & Dirty**  
- Exploit vulnerable validators simultaneously.  
- Delete validator keys/data or kill processes.  
- Network instantly loses ~40% voting power.  
- Consensus halts once safety threshold (<66.7%) is breached.  

**Option B Stealthy**  
- Gradually compromise validators, install persistence/backdoors.  
- Wait for an optimal moment (major transfer, high-value event).  
- Trigger disruption: either halt the network or manipulate consensus.  

### Real-world scenario
- Compromising ~30 of the highest-voting-power vulnerable validators could cross the 33.4% Byzantine threshold.  
- Cost: tens of thousands of dollars in cloud resources and a week of prep.  
- Impact: network halted for days, multi-billion market cap destabilized.  
- Once an attacker is in, that’s it — recovery could take weeks if keys are unencrypted or backups are missing.  

### Why it matters
This isn’t hypothetical — these are **real, exploitable CVEs and open SSH/Docker ports**. Any competent attacker with moderate resources could carry this out. By looking at the network holistically rather than isolated nodes, it’s clear the system as a whole is below safe Byzantine thresholds.  

**Timeline Risk**: Based on current exploit activity observed in the wild, it seems almost impossible that one of these validators has not already been compromised.

## Prioritized Remediation Plan

### Immediate Actions (24-48 hours)

- **Critical**: Patch or restrict ALL services with known CVEs, not just SSH
- Focus on the **highest voting-power validators** first to reduce at-risk stake below 3,334
- Deploy emergency firewall rules to restrict non-essential ports
- **Verify backup procedures work** - many validators have never tested recovery
- **Encrypt all private keys at rest** - assume compromise is imminent

### Short-term (1 week)

- Implement principle of least privilege: only p2p port should be publicly accessible
- Deploy intrusion detection/prevention systems
- Enable automated security patching
- Audit all exposed services for necessity
- **Conduct internal security audits** (our external scan is just the tip of the iceberg)

### Verification

- We will re-scan in 72 hours to verify critical fixes
- Weekly automated scans thereafter to track improvement
- Provide ongoing metrics to track network security posture

## Technical Support Offered

As part of PGDN's mission to improve decentralized network security, we can provide:

- Detailed remediation guides for each vulnerability class
- Direct technical support for critical validators
- Automated scanning API for continuous monitoring
- Security workshop for validator operators
- Access to PGDN platform for ongoing security monitoring (validators can claim their nodes securely)

**Final Assessment:** Sui's validator infrastructure currently operates below the security threshold required for Byzantine fault tolerance. Immediate action is required to prevent potential network disruption.

**About PGDN:** PGDN is currently in early access, having just launched our validator security monitoring platform. Our mission is to improve the security posture of decentralized networks through transparent, continuous security assessment. We publish anonymized security metrics on-chain while providing detailed, actionable intelligence to validators who verify ownership of their nodes.

*This repo documents our methodology for reproducibility and integrity. The analysis represents a point-in-time assessment and actual security posture may have changed.*

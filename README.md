# Sui Validator Security Analysis

## Critical Finding: Consensus Liveness at Risk

**TL;DR: We found enough vulnerable validators to potentially halt Sui. 39.6% of voting power (3,955/10,000) had critical vulnerabilities on 18/08/2025. Network halts at 33.4% (3,334). Margin: 621 voting power.**

Our analysis reveals that Sui's validator infrastructure has sufficient attack surface to be pushed below the Byzantine fault tolerance threshold. Through external scanning alone, we identified:

- **39.6% of voting power** externally exploitable via SSH exposure and/or known CVEs (validators only)
- Network was **only 621 voting power away** from consensus failure
- These are **public-facing vulnerabilities only** - internal security is likely worse considering findings
- Recovery from a coordinated attack could take **days to weeks**

**This is not theoretical** - the current validator exposures cross the line where a chain halt becomes possible. One coordinated attack exploiting these known vulnerabilities could result in a multi-day network outage. Moreover, our findings came from analysing public ports, not the entire range. It’s expected that the misconfiguration issues are even wider.

We estimate that within a week, the entire Sui network could be taken offline.

[Full technical analysis and methodology below and in the [simulated_attack.md](simulated_attack.md)]

## Network-Wide Security Posture

**Aug 2025, PGDN.ai analyzed the Sui network. Scanning 122 nodes** (118 validators, 4 RPC):

- **39.6%** of voting power has critical vulnerabilities (SSH/CVEs)
- **28.6%** running CVE-affected services
- **38.5%** SSH publicly reachable
- **31.9%** serving websites (validators shouldn't)
- **37%** only displaying public metrics
- **95.1%** no detectable WAF
- **99%** with port 2375 exposed (Docker port; **port-only** check)
- **Average PGDN score: 44/100** (critical range)

**Methods:** Service discovery, banner + protocol handshakes, conservative CPE→CVE mapping. External scanning only - internal security likely worse. Findings are aggregated and anonymized.

This repository is a factual record. Operators can reproduce category checks without disclosing identities. Content hashes published for integrity.

A visual heatmmap displaying the issues can be found on [https://pgdn.ai/heatmap](https://pgdn.ai/heatmap)

**Contact:** Simon (sm@pgdn.ai) for private remediation support.

## Responsible Disclosure Timeline

Note: Initial disclosure attempts revealed fundamental misunderstandings about infrastructure security. The team appeared to believe that secure validator communications meant other exposed services weren't vulnerabilities. A CVE is a vulnerability regardless of which service or port it affects.

- **2025-08-02:** Initial contact via intermediary to Sui security (SSH, CVE summary only)
- **2025-08-05:** Followup contact via intermediary with Sui security (SSH, CVE summary only)
- **2025-08-15:** Discord #dev post requesting staff ticket (no info disclosed) - mostly ignored
- **2025-08-15:** Follow-up - mostly ignored

## About PGDN - Why This Exists

After 4+ years protecting DeFi and 20 years protecting networks, I kept seeing the same pattern: teams pour time and budget into **smart-contract risk** (audits, bounties, formal proofs) and **offload responsibility to auditors** - while **internet-facing infrastructure** is left to drift (ports, RPC, metrics, admin planes).

**Passing an audit ≠ securing a network.**

Recent interactions with Sui reinforced that gap. There's often a fundamental misunderstanding that if validator consensus communication is encrypted, other exposed services don't matter. This is dangerously wrong. Every exposed service is an attack vector. A CVE on ANY port can lead to full compromise. Our goal is to prevent these misunderstandings from becoming disasters.

### What [pgdn.ai](http://pgdn.ai/) Analyses

- DeFi/DePIN **networks** - not just validators
- Components like **validators/fullnodes, RPC gateways, indexers, bridges, sequencers, oracles, staking pools**, plus related **management/metrics** surfaces
- Byzantine fault tolerance thresholds and consensus risk

### How We Operate (Safety First)

- **No exploitation or stress-testing.** We observe what's publicly reachable and quantify risk. The objective is **hardening**, not disruption
- Nodes are **analysed and scored** using a network-agnostic model so ecosystems can be compared fairly
- We publish **anonymised scores on-chain** (with integrity hashes) to create an **indisputable audit trail** without exposing operators
- Validators can **claim their nodes** through secure verification to access detailed findings
- We provide **operator self-checks** and a straightforward **remediation checklist** on request
- **Optional, opt-in "simulated attack".** When an owner explicitly asks, we run a **scoped, non-destructive simulation** to demonstrate impact paths and validate fixes. This is consent-based, rate-limited, and designed not to degrade service

### What We Don't Do

- We don't dump IPs/hosts or name operators in public artifacts
- We don't run attacks without explicit operator consent
- We don't publish PoCs that could enable opportunistic abuse
- We don't disclose specific CVE details publicly

### Why This Matters

Making exposure visible - then **coaching fixes** - is the fastest way to keep networks online and trustworthy. That's the service we offer: measurable posture, accountable publication, and practical remediation that hardens the entire ecosystem.

## Platform Status

PGDN was founded in July 2025 and is still in **early access, invite only**. If you're Sui staff and want a quick sanity check before broader publication, please reach out.

**Contact:**

Simon ([pgdn.ai](http://pgdn.ai/))
[github.com/simonmorley](http://github.com/simonmorley)
[linkedin.com/in/simonpmorley](https://linkedin.com/in/simonpmorley)

## Documentation

- [Simulated Attack](simulated_attack.md)
- [Responsible Disclosure Timeline](timeline.md)
- [Externally Observable Posture Findings](findings.md)
- [Limitations, Assumptions & Review Status](limitations.md)
- [Methodology](methodology.md)
- [Remediation](remediation.md)
- [Project Status](status.md)
- [Contact](contact.md)
- [Security](security.md)
- [Roadmap](roadmap.md)
- [Validation](validation.md)

*This analysis represents a point-in-time assessment. Actual security posture may have changed. All scans were conducted under responsible disclosure principles with no exploitation or service disruption.*

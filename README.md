# Sui Validator Security Analysis

## Critical Finding: Consensus Liveness at Risk

**TL;DR:** External scans of the Sui network (Aug 2025) show that **~40% of voting power exposes SSH or default web servers, or runs CVE-affected services**.

Consensus halts if ~33.4% of voting power is disrupted. At the time of scan, **3,955 voting power (~39.6%) was exposed vs. 3,334 required to halt** — leaving only a 621 voting power margin.  

This is a **credible systemic risk**. One coordinated attack timed with a new SSH or web CVE could result in a multi-day outage affecting billions in assets.  

Key exposures observed:
- **39.6% voting power** externally exposed via SSH and/or CVEs  
- **28.6%** running CVE-affected services  
- **38.5%** SSH publicly reachable  
- **31.9%** serving websites (validators should not)  
- **37%** exposing public metrics  
- **95.1%** without a detectable WAF  
- **~99%** with 2375/tcp responding (Docker-TCP port; port-only, protocol unconfirmed → **LOW confidence**)  
- **Average PGDN score: 44/100** (critical range)  

[Full technical analysis and methodology below and in the [simulated_attack.md](simulated_attack.md)]

## Recent Interactions & Response

We disclosed these findings privately to Mysten Labs  & Sui in August 2025.  

- Mysten acknowledged SSH and Apache exposure but stated these were "hygiene, not vulnerabilities."  
- They confirmed Mysten's own two validators are patched, but did not dispute our fingerprinting of exact OS and OpenSSH versions.  
- They said 80/443 exposure was "intentional" for RPC, and claimed they "could not find 2375 open."  
- They emphasized Mysten does not manage independent validators and pointed to the bug bounty program (which does not cover posture).  
- No baseline, no timeline, and no guidance for operators was provided.  
- Separately, their legal team instructed our contact inside Mysten that he could no longer discuss this with us.  

**Our position:** Hygiene and OPSEC exposures are not cosmetic. They are the *entry points* attackers chain into consensus-level disruption. In decentralized systems, the absence of a minimum baseline leaves the entire network at risk.

## Why Hygiene & OPSEC Matter

- **Version Fingerprinting**  
  Public SSH and HTTP banners leak exact Ubuntu/OpenSSH versions. Even patched, this gives adversaries pre-built target lists the moment a new CVE drops.  

- **Default Web Landers**  
  Dozens of validators serve default Apache pages. This signals misconfiguration, provides proof-of-life to bots, and leaks headers that map build chains and CDNs.  

- **SSH Reachability**  
  Exposed SSH is the most abused management surface in history. Brute-force, leaked keys, or zero-days can all ride this port. Patched today ≠ safe tomorrow.  

- **Unexplained Open Ports (2375/tcp)**  
  SYN/ACK seen across most validators. Protocol unconfirmed, but such uniform patterns suggest latent exposure or misconfiguration. Attackers will not ignore it.  

- **Cumulative Risk**  
  One exposed node is an accident. 40% is systemic. With only a one-third threshold for consensus liveness, hygiene exposures at this scale create real financial risk.

### Default Web Landers Misinterpreted

Mysten Labs responded that 80/443 exposure was "intentional" for RPC. However, many validators are serving **default Apache landing pages** on those ports — many with **critical CVEs**.

This is not RPC — it is a misconfigured web server. Treating default landers as "intended RPC endpoints" is a fundamental misunderstanding of hygiene. Also, Sui validators should NOT be running webservers!

Confusing a stock Apache index page with an RPC endpoint underscores why **independent posture analysis** is essential: without it, basic hygiene issues get dismissed as non-issues.

## Responsible Disclosure Timeline

- **2025-08-02** – Initial contact via intermediary to Sui security (SSH, CVE summary only)  
- **2025-08-05** – Follow-up via intermediary with same categories  
- **2025-08-15** – Discord #dev post requesting staff ticket (no info disclosed) – ignored  
- **2025-08-18** – Formal disclosure email to Mysten Labs (SSH, CVEs, simulated attack scenario)  
- **2025-08-21** – Mysten requested GitHub access to review findings; limited dataset shared  
- **2025-08-22** – Mysten response: hygiene ≠ vuln; 80/443 intentional; 2375 not observed  
- **2025-08-24** – PGDN follow-up: confirmed Mysten fingerprinting; raised OPSEC issue; reiterated questions  
- **2025-08-26** – Mysten final note: no baseline, "we don't manage independent validators"; offered to "pass along general messages"  
- **2025-09-03** – No further reply; public disclosure via this repo

## Follow-Up: Subsequent Findings and Clarifications

- **Mysten validators** fingerprint as Ubuntu 22.04 / OpenSSH 8.9p1. CVEs exist upstream, but backports appear patched. Issue = OPSEC, not active vuln.  
- **Port 2375** – Widespread SYN/ACK responses observed. Protocol unconfirmed → **LOW confidence**. Excluded from exploitability math.  
- **Policy questions** (is SSH acceptable, are default web landers acceptable, what explains 2375) remain unanswered.  
- **No baseline** exists for independent validators.  

**Conclusion:** Systemic hygiene and OPSEC exposures across ~40% of the set represent a credible liveness risk. Without minimum standards, the network remains vulnerable to opportunistic or coordinated disruption.

## About PGDN – Why This Exists

After 20 years in network security and 4+ years protecting DeFi, the same pattern repeats:  
teams focus on **smart-contract audits** while **internet-facing infrastructure drifts open** (ports, RPC, metrics, admin planes).  

**Passing an audit ≠ securing a network.**

PGDN exists to measure **outside-in posture** of DeFi/DePIN infrastructure, quantify systemic risk, and provide actionable remediation.

## How We Operate (Safety First)

- **No exploitation.** External observation only  
- **Network-agnostic scoring** for cross-ecosystem comparison  
- **On-chain publication** of anonymised scores for accountability  
- **Operator self-checks + remediation checklist** available on request  
- **Optional simulated attack** only with explicit operator consent (scoped, non-destructive)

## Why This Matters

Making exposure visible — then **coaching fixes** — is the fastest way to keep networks online and trustworthy. Hygiene is not cosmetic; it is the difference between resilience and outage.

## Platform Status

PGDN was founded in July 2025 and is in **invite-only early access**.  
For Sui staff or operators wanting private remediation guidance, contact:

**Simon Morley** – [pgdn.ai](http://pgdn.ai) | [github.com/simonmorley](http://github.com/simonmorley) | [linkedin.com/in/simonpmorley](https://linkedin.com/in/simonpmorley)

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

---

*This analysis represents a point-in-time snapshot. Posture may have changed. All scans conducted under responsible disclosure principles, without exploitation or service disruption.*  

Licensing: CC BY-NC-ND 4.0 — journalists may quote with attribution to **PGDN**; commercial reuse and derivative datasets prohibited.

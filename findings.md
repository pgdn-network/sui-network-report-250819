# Externally Observable Posture Findings

**Snapshot:** Sui mainnet • **Window:** Aug 2025

**Scope:** 122 total (118 validators, 4 public rpc) • **Method:** external observation only; no exploits

## Summary (counts & % of 122)

| Category | Test (how it was established) | Count | % of N | Confidence |
| --- | --- | --- | --- | --- |
| CVE‑affected services | Version → CPE → CVE map (see methodology) | 35 | 28.6% | High |
| SSH reachable (22/tcp) | TCP open + SSH handshake banner | 47 | 38.5% | High |
| No WAF on HTTP endpoint(s) | Negative WAF signatures across detectors | 116 | 95.0% | Medium |
| Public websites / landing pages | Manual verification | 39 | 31.9% | Medium |
| Public metrics | HTTP 200 on metrics path; not localhost‑bound | 45 | 36.8% | Low |
| Possible Docker TCP (port 2375 open) | 2375/tcp in OPEN state (port-only) | 121 | 99.2% | Low |

> Notes: “No WAF” was evaluated only where an HTTP‑facing endpoint exists.
> 
> “Suspected Docker TCP” reflects what was recorded in the dataset; verification details in [**methodology.md**](http://methodology.md/).
> 
> Websites were detected on ports 80 or 443 only.

**Network posture score:** average **44 / 100** (range **15–93**). This is alarmingly low.

> *Docker note:* counts are **port-only** observations for 2375/tcp. No protocol fingerprinting was performed in this snapshot; false positives are possible.

## Definitions

- **CVE‑affected** = Service version confidently mapped to one or more CVEs (confidence policy in methodology).
- **Public metrics** = Sui metrics endpoint reachable from the internet .

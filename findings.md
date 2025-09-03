# Externally Observable Posture Findings

**Snapshot:** Sui mainnet • **Window:** Aug 2025  
**Scope:** 122 total (118 validators, 4 public RPC)  
**Method:** external observation only; no exploits

## Summary (counts & % of 122)

| Category | Test (how it was established) | Count | % of N | Confidence |
| --- | --- | --- | --- | --- |
| CVE-affected services | Version → CPE → CVE map (see methodology) | 35 | 28.6% | High |
| SSH reachable (22/tcp) | TCP open + SSH handshake banner | 47 | 38.5% | High |
| No WAF on HTTP endpoint(s) | Negative WAF signatures across detectors | 116 | 95.1% | Medium |
| Public websites / landing pages | Manual verification | 39 | 31.9% | Medium |
| Public metrics | HTTP 200 on metrics path; not localhost-bound | 45 | 36.8% | Low |
| Possible Docker TCP (2375/tcp) | SYN/ACK responses observed; protocol unconfirmed | 121 | 99.2% | Low |

**Network posture score:** average **44 / 100** (range **15–93**). This is alarmingly low.

## Notes

- **CVE-affected services**: Some of these are patched Ubuntu builds (backports applied). The 28.6% figure is therefore a conservative **upper bound** and may come down with operator verification.  
- **Public websites**: Many of these were default Apache landing pages, some advertising versions with **known critical CVEs**. Treating them as "intended RPC" is incorrect; they are misconfigured web servers.  
- **Docker TCP (2375/tcp)**: Counts are **port-only** observations. We observed SYN/ACK across most validators, but no protocol handshake was confirmed. These are excluded from exploitability math. The widespread open state remains unexplained.  
- **No WAF**: Only evaluated where an HTTP endpoint exists.  
- **Public metrics**: Defined as Sui metrics endpoint reachable from the internet, not bound to localhost.  

## Definitions

- **CVE-affected** = Service version confidently mapped to one or more CVEs (confidence policy in methodology).  
- **Public metrics** = Sui metrics endpoint reachable from the internet.  


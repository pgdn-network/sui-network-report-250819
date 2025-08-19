# Remediation

**Goal:** minimize externally reachable attack surface; require auth on management planes; patch vulnerable services.

## Priorities

| Pri | Category | Indicator (outside‑in) | Owner | Target state | Verify (outside‑in) |
| --- | --- | --- | --- | --- | --- |
| P1 | SSH broad exposure | 22/tcp reachable from internet | Infra/Sec | Key‑only auth; source IP allowlist (bastion); rate‑limit | Only bastion/VPN source reaches 22/tcp |
| P1 | CVE‑affected service | Version maps to critical CVEs | App/Infra | Patch/upgrade or mitigate (WAF/deny) | Version no longer maps to CVEs |
| P2 | Docker TCP exposure | 2375/tcp open or unfiltered | Infra | Disable TCP; or enforce **mTLS + IP allowlist** | 2375/tcp closed or requires mutual TLS |
| P3 | No WAF on HTTP | Limited WAF signatures | Sec/Infra | Add L7 filtering where applicable | WAF detectable; baseline rules active |
| P3 | Ensure Metrics Open | Inconsistent metrics endpoints | App | Ensure consistent metrics responses | |

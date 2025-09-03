# Responsible Disclosure Timeline

| Date (UTC) | Channel | To / From | What | Outcome |
|---|---|---|---|---|
| 2025-08-02 | Private (intermediary) | Sui security | First posture summary (SSH exposure, default web landers, CVEs) | Acknowledged; ownership unclear. |
| 2025-08-05 | Private (intermediary) | Sui security | Follow-up with same categories | Acknowledged; CVE applicability disputed. |
| 2025-08-05 | Discord #dev | Mods/Staff | Asked for security owner; shared anonymised context | Picked up but not actioned. |
| 2025-08-18 | Email | security@sui.io | One-pager + simulated attack note; asked for owner + timeline | — |
| 2025-08-21 | Email | Mysten (Rahul) → PGDN | Asked for GitHub access to review CVEs | Access granted; minimal dataset shared.  |
| 2025-08-22 | Email | Mysten (Rahul) → PGDN | Framed SSH/Apache as hygiene; 80/443 intentional for RPC | Policy questions unanswered.  |
| 2025-08-24 | Email | PGDN → Mysten | Confirmed Mysten hosts fingerprint (Ubuntu/OpenSSH patched); OPSEC concern raised | Acknowledged; no baseline offered.  |
| 2025-08-26 | Email | Mysten (Rahul) → PGDN | “Hygiene ≠ vulnerabilities”; “we don’t manage independent validators”; “could not find 2375 open”; bounty pointer | No plan/timeline; offered to pass along “general messages.”  |
| 2025-08-27 | Email | PGDN → Mysten | 2375 follow-up with suggested local checks; asked for operator contact path | —  |
| 2025-09-03 | Publication | PGDN | Public aggregate report (this repo) with OPSEC + 2375 clarifications | TLP:CLEAR aggregate only; operators can request per-node details. |

# Methodology

## Dataset construction

- **Discovery sources:** on‑chain/registry data and public lists for Sui validators. 118 validators were discovered, we included a few public rpc & hybrid nodes.
- **Target roles:** validators (this snapshot). 4 RPC nodes were included for a benchmark but not included in the simulated attack. Other network roles (indexers/bridges) are out of scope for this file.
- **De‑dup policy:** collapse multiple IPs per validator for category counts; retain per‑IP during verification.
- **Vantage points:** multiple regions/ASNs; bounded concurrency; exponential backoff on flaps.
- **Time bounding:** observations within the Aug 2025 window.

## Verification chain (per endpoint)

1. **Banner:** identify protocol/service (SSH/HTTP/gRPC/etc.).
2. **Handshake:** protocol‑correct exchange (e.g., SSH KEX; HTTP GET; Docker `/_ping`).
3. **Semantic check (where applicable):** e.g., metrics page parses.
4. **State rules:** count **open** only; exclude `filtered`/`closed`; re‑try on transient errors.
5. **Recording:** store raw observation, normalized findings and confidence label.
6. **Manual checking**: analysis was run multiple times over a few weeks to verify results.

## CVE mapping

The CVE database was downloaded from [nist](https://services.nvd.nist.gov/rest/json/cves/2.0) in July 2025.

- **Version extraction:** via nmap and other tools, including service banners, HTTP headers/pages.
- **CPE selection:** exact match preferred; otherwise fuzzy with threshold.
- **Confidence policy:**
    - **High** - exact version/build matched; multiple signals agree.
    - **Medium** - major.minor matched; patch unknown or single signal.
    - **Low** - heuristic only (not used in headline counts).
- **Scoring of CVEs:** based on CVSS; aggregated per host/service for posture scoring.

## WAF/TLS checks (HTTP endpoints)

- **TLS:** client hello and HTTP probe; counted "No TLS" only when clear‑text 200 observed.
- **WAF:** multiple detectors (e.g., header/behavioral signatures). "No WAF" = no detectable WAF signature; not proof of absence.

## Docker TCP (2375) checks

- **Reachability:** we only recorded **2375/tcp in OPEN state** (TCP SYN/ACK).
- **No protocol fingerprinting:** this snapshot **did not** attempt `/_ping`, `/version`, or banner grabs.
- **Interpretation:** classify as **Possible Docker TCP exposure (port-only)** with **Low** confidence. This can include false positives (port reuse/decoys/middleboxes).
- **Roadmap:** future snapshots will add endpoint checks and TLS/mTLS detection to upgrade confidence.

### Port 2375 clarification

- This snapshot recorded **OPEN state on 2375/tcp** for many targets, but **did not** confirm Docker-TCP via protocol handshake.
- We therefore classify 2375 as **LOW confidence** ("port-only") and **exclude it from exploitability math**. See `appendix_2375.md`.

s cWe asked Sui to 

## Scoring

**What it is.** A 0–100 posture score that summarizes outside-in risk for a target, using the same rules across networks. It lets readers compare, e.g., Sui vs. Aptos, without network-specific bias.

**Library status.** We’re preparing to publish the scoring library. For now the score is computed by a generic, network-agnostic service that consumes our normalized findings.

**Model (brief).**

- Start at **100**; subtract weighted penalties for confirmed findings.
- Weights reflect risk and prevalence; they’re the same across networks.
- Scores roll up per target, then can be averaged per role/network.

**Factors used (current snapshot).**

- **CVE exposure:** penalty scales with CVSS (e.g., Critical > High > Medium). Requires version confidence ≥ *Medium*.
- **Sensitive management surfaces:** SSH reachable from the internet, public metrics, etc.
- **Transport & edge:** lack of TLS where an HTTP role is expected; no detectable WAF on public HTTP endpoints (small weight).

**Confidence handling.**

- Findings labeled **Low** confidence are **excluded** from the score by default (e.g., 2375/tcp "port-only" observations in this snapshot).
- **Medium/High** confidence findings are scored; higher confidence may carry slightly higher weight.

**Normalization for cross-network comparison.**

- Role-aware expectations (e.g., RPC vs. validator peer ports) avoid penalizing necessary, non-HTTP peer traffic.
- Identical weights and thresholds across networks enable side-by-side comparisons (e.g., Sui vs. Aptos).

**Output.**

- Per-target score (**0–100**), plus **network averages** and **distribution (min/avg/max)** used in the Findings summary.

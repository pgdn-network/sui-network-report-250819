# Limitations, Assumptions & Review Status

This snapshot reflects what an unauthenticated internet client could observe during **Aug 2025**. It is **outside-in analysis only** - no credentials, no exploits, no traffic amplification.

## What this is / isn’t

- **Is:** A point-in-time view of publicly reachable services and their properties (e.g., version banners, TLS behavior, metrics exposure).
- **Isn’t:** Proof of exploitability, internal configuration audits, or business-logic testing. Private networks and authenticated paths are out of scope.

## Where false positives can occur

1. **Version inference → CVE mapping**
Banners/headers can be wrong, proxied, or masked; minor/patch versions affect CVE relevance.
2. **Port identity confusion**
An open port number is **not** proof of a specific service (e.g., anything can bind to 2375/tcp).
3. **Middleboxes / edges**
CDNs, WAFs, NAT, and custom proxies can alter headers or responses.
4. **Ephemeral state**
Ports and configs change quickly; a host may flap between open/closed during the window.
5. **WAF/TLS detection limits**
“No WAF” means no **detectable** signature; “No TLS” applies **only** where an HTTP role is expected.

## How we reduced false positives

We applied automated checks and **manual analyst review** before counting headline findings.

- **Protocol handshake:** After initial bannering, we attempted a protocol-correct exchange (e.g., SSH KEX; HTTP GET;).
- **Semantic checks:**
    - *Metrics:* required a parsable metrics page or role-consistent content (not just a 200).
    - *CVE mapping:* required corroborating version signals; low-confidence matches excluded from headline counts.
    - *Docker 2375:* **no endpoint checks in this snapshot**; Docker exposure remained **port-only** and was labeled **Low** confidence.
- **Multiple detectors:** WAF checks used more than one signature source; only consistent negatives were counted.
- **Re-scans on flaps:** Selected hosts were re-tested within the window to confirm stability of a finding.
- **Confidence labels:** Each mapped service/CVE received **High/Med/Low** confidence. **Low** confidence items were excluded from headline counts.
- **De-dup & attribution rules:** We collapsed multi-IP per operator for category counts and avoided mixing anycast/frontends with backends where role evidence was weak.
- **Human-in-the-loop:** Sensitive categories (e.g., suspected Docker TCP, public metrics) were manually reviewed with captured responses before inclusion.

Residual risk remains (e.g., deceptive banners, transient changes), but the measures above materially reduce false positives versus naïve port/banners scans.

## Anonymisation & ethics

- Public artefacts are **aggregate and anonymised**.
- We will only share data with the node operators.

## Independent review status (Sui)

We requested a quick sanity check from Sui security/infra twice during Aug 2025. The responses revealed fundamental misunderstandings about CVEs and exposed services being security risks regardless of secure validator communications. Messages on Discord were also misunderstood.

## Reproduction & disputes

- Anyone can reproduce category checks on **their own assets** using the methodology described.
- Operators who believe a finding is misattributed or outdated can use the **VALIDATION** and **CONTACT** flows to prove ownership and submit a **Dispute**; we will re-check and update.

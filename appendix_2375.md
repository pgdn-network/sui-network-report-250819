# Appendix: Port 2375 (Docker-TCP) – What We Saw

**Summary:** Multiple vantage points observed **SYN/ACK** on 2375/tcp across a large share of targets. Attempts to confirm Docker-TCP via `GET /_ping` or `/version` did **not** return canonical Docker responses.

- **Classification:** LOW confidence. We treat this as an **unexplained open/filtered port** pattern. It is **not** counted as confirmed Docker exposure.
- **What would raise confidence:** A successful `/version` handshake, TLS client-auth prompts, or server banners consistent with Docker-TCP.
- **Operator self-check:** `sudo ss -tlnp | grep :2375` and `:2376` on your validator. If present, bind to localhost and require mTLS or close entirely.

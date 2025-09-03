# Reproducibility (Ethical, Low-Impact)

> **Only test assets you own/control.** These examples are for operators validating their own posture.

- **SSH banner check:** `nc -v <host> 22` → expect `SSH-2.0-OpenSSH_...`
- **Default web lander:** `curl -I http://<host>` → look for `Server:` and default index pages.
- **WAF heuristics:** `curl -I https://<host>` and simple atypical header checks (no signature → “no WAF detected”).
- **Metrics exposure:** `curl -I http(s)://<host>:9100/metrics` or known Sui metrics path (200 vs 403/localhost only).
- **2375 probe (non-intrusive):** `printf 'GET /_ping HTTP/1.0\r\n\r\n' | nc -v <host> 2375` → **do not** brute-force; absence of canonical response ⇒ keep **LOW** confidence.

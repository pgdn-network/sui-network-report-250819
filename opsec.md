## Why Hygiene Matters (OPSEC)

These are not theoretical quibbles. The exposures observed across Sui's validator set create a map that attackers can operationalize:

- **Version Fingerprinting**  
  Public SSH and HTTP banners disclose exact Ubuntu and OpenSSH versions. Even if patched, this gives adversaries pre-built target lists the moment a new CVE drops. Exploit kits and botnets will filter on those banners automatically.

- **Default Web Landers**  
  Dozens of validators serve default Apache or Nginx index pages on port 80/443. This signals misconfiguration, provides proof-of-life for scanning bots, and often leaks headers that reveal build chains, CDN use, or admin panels behind weak access controls.

- **SSH Reachability**  
  Exposed SSH is not "just hygiene." It is the most abused management surface in history. Brute-force, credential stuffing, leaked keys, or supply-chain backdoors can all ride that port. A patched daemon today does not protect against tomorrow's zero-day.

- **Unexplained Open Ports (2375)**  
  Widespread SYN/ACK on Docker-TCP's well-known port is a red flag. Even if it is a false positive, the pattern itself suggests uniform misconfiguration or latent exposure. Attackers will not care about the semantics — they will probe until something gives.

- **Cumulative Risk**  
  One exposed node can be an accident. Dozens across 40% of the set is systemic. In proof-of-stake networks, halting liveness takes only one-third of voting power. Hygiene leaks accelerate attacker dwell-time, coordination, and success probability.

**Bottom line:**  
Operators should assume that anything visible externally will be fingerprinted, indexed, and weaponized within hours of a new exploit release. "Hygiene ≠ vulnerability" is a false dichotomy — hygiene *is* the difference between resilience and total loss.

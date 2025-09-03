# Appendix: Port 2375 (Docker-TCP) – What We Saw

**Summary**  
Across multiple vantage points, we consistently observed **SYN/ACK responses on 2375/tcp** for a majority of Sui validators.  
This is the well-known Docker-TCP management port.  

**Mysten's claim:**  
In private correspondence, Mysten Labs stated they "could not find any instances of 2375 being open."  
This is demonstrably incorrect — anyone can confirm with a simple `nc` or `telnet` from outside the network.

**Protocol check:**  
We attempted non-intrusive probes (`/_ping`, `/version`) and did **not** receive canonical Docker responses.  
Because no handshake was confirmed, we classify these results as **LOW confidence** ("port-only").  
This category is **excluded** from exploitability math.

**The gap:**  
- The Sui documentation does not list 2375 as a required port.  
- Mysten has provided no explanation for what is actually bound to 2375/tcp.  
- If intentional, no rationale has been published.  
- If unintentional, it represents a **fleet-wide misconfiguration**.  

**Why it matters:**  
2375 is historically one of the most abused remote-management ports in cloud environments.  
Even absent Docker, the uniform open state across validators is a red flag:  
- Attackers will probe until a handshake is found.  
- If it *is* Docker, unauthenticated remote API = full host takeover.  
- If it's something else, operators deserve to know what and why.  

**Pattern of Dismissal**  
This is not the first time exposures have been reframed incorrectly.  
- Mysten described default Apache pages on 80/443 as "intentional RPC." They are not. They are **CVE-affected Apache servers** left exposed on validator hosts.  
- Mislabeling vulnerable services as "intended" endpoints underscores why independent posture analysis is required: otherwise, basic hygiene issues get waved away while real risk persists.

**Operator self-check:**  
On your validator host, run:  
```bash
sudo ss -tlnp | grep :2375
sudo ss -tlnp | grep :2376



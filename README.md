# BotsV2-Splunk

# Incident Response & Threat Hunting Report: Multi-Stage Enterprise Breach Analysis

## Executive Summary
During a threat hunting exercise using Splunk (BOTS v2 dataset), an end-to-end multi-stage intrusion was identified and analyzed. The attacker executed web application reconnaissance, targeted spear-phishing, client-side XSS exploitation, server-side account escalation, and persistent encrypted Command & Control (C2) beaconing.

---

## Attack Chain Timeline

1. **Reconnaissance (SQL Injection):**
   * **Attacker IP:** `45.77.65.211`
   * **Target:** `gacrux` (`172.31.4.249`) running MyBB (`brewertalk.com`).
   * **Vector:** Error-based SQL injection targeting `/member.php` using `updatexml()`.

2. **Pre-Attack Tracking & Session Leakage:**
   * **Target Host:** `wrk-klagerf` (`10.0.2.109`)
   * **Vector:** `GET /` request on attacker port `9999` leaking MyBB forum session cookies (`mybb[lastvisit]`, `mybb[lastactive]`).

3. **Phishing & Client-Side Exploitation:**
   * **Sender:** `frankesters48@gmail.com` (`136.0.2.138`)
   * **Recipient:** Kevin Lagerfield (`klagerfield@froth.ly`)
   * **Vector:** Malicious link containing an embedded Reflected XSS payload launching Chrome on workstation `wrk-klagerf`.

4. **Privilege Escalation & Backdoor Creation:**
   * **Vector:** CSRF via Reflected XSS targeting `/admin/index.php`.
   * **Artifact:** Silent creation of backdoor administrator account `kIagerfield`.

5. **Command & Control (C2) Beaconing:**
   * **C2 Server:** `45.77.65.211:443`
   * **Traffic Analysis:** Over 5,600 TCP streams and 11,000+ Suricata IDS alerts (`SURICATA TLS invalid handshake message` / `invalid record/traffic`), confirming custom-encrypted C2 communication over port 443.

---

## Indicators of Compromise (IoCs)

| Category | Indicator Value | Description / Context |
| :--- | :--- | :--- |
| **Attacker IP** | `45.77.65.211` | SQLi Recon, Cookie Theft (Port 9999), Encrypted C2 (Port 443) |
| **Attacker IP** | `136.0.2.138` | Phishing Email Origin & Backdoor Login |
| **Phishing Sender** | `frankesters48@gmail.com` | Malicious email sender |
| **Rogue Admin Account**| `kIagerfield` | Created via CSRF/XSS on MyBB (`gacrux`) |
| **Compromised Host** | `wrk-klagerf` (`10.0.2.109`) | Kevin Lagerfield's workstation running C2 agent |
| **Vulnerable Server** | `gacrux` (`172.31.4.249`) | Web application hosting `brewertalk.com` |

---

## Defensive Recommendations
* **Perimeter Blocking:** Immediately block IPs `45.77.65.211` and `136.0.2.138` on firewalls/web proxies.
* **Host Isolation:** Isolate `wrk-klagerf` (`10.0.2.109`), revoke credentials for user `kevin.lagerfield`, and re-image the endpoint.
* **Application Patching:** Patch XSS in `/admin/index.php` and SQLi in `/member.php`.
* **Account Cleanup:** Remove rogue administrator `kIagerfield` from the database.

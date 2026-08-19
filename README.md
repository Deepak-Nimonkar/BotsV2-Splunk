#  Splunk BOTSv2 DFIR Investigation: Comprehensive Web Exploitation & Enterprise-Wide Fileless Malware Analysis

##  Executive Summary
During an advanced Digital Forensics and Incident Response (DFIR) investigation into the **Splunk BOTSv2 (Boss of the SOC v2)** dataset, two interconnected attack campaigns targeting **Frothly** (`brewertalk.com` & internal corporate Active Directory infrastructure) were identified and reconstructed.

1. **Campaign A (Web Application & Client-Side Compromise):** An external threat actor (`45.77.65.211` / `136.0.2.138`) executed error-based SQL Injection, session cookie exfiltration, and a targeted spear-phishing campaign leveraging Reflected XSS. The payload bypassed anti-CSRF protections via direct DOM extraction to silently create a rogue administrator account (`kIagerfield`).
2. **Campaign B (Endpoint Weaponization, Fileless Persistence & Lateral Movement):** A weaponized Word document (`invoice.doc` inside `invoice.zip`) executed on endpoint `wrk-btun` spawned encoded PowerShell loaders, staged obfuscated payloads directly inside the Windows Registry (`HKLM\Software\Microsoft\Network\debug`), established persistence via `schtasks.exe`, and propagated laterally across multiple corporate assets (`venus`, `wrk-klagerf`, `mercury`).

---

## 🗺️ Complete Multi-Stage Attack Architecture

```text
========================================================================================
CAMPAIGN 1: WEB EXPLOITATION & CLIENT-SIDE CSRF BACKDOOR (Target: gacrux / wrk-klagerf)
========================================================================================
[45.77.65.211] ──► SQLi (updatexml) on /member.php
       │
[45.77.65.211:9999] ◄── Cookie Leakage (mybb[lastvisit], mybb[lastactive]) from wrk-klagerf
       │
[136.0.2.138] ──► Spear-Phishing Email (frankesters48@gmail.com) to klagerfield@froth.ly
       │
[Admin Clicks Link] ──► Chrome executes Reflected XSS (utid=2) on MyBB Admin CP
       │
[DOM Scraped] ──► Extracts `my_post_key` ──► Asynchronous XMLHttpRequest POST
       │
[Rogue Admin Created] ──► `kIagerfield` (Homograph 'I') created with `usergroup=4`
       │
[Attacker Auth] ──► 136.0.2.138 logs in via NaenaraBrowser (Red Star OS) ──► Alters avatar path
       │
[C2 Channel] ──► Beaconing to 45.77.65.211:443 (Suricata TLS Invalid Handshake Alerts)

========================================================================================
CAMPAIGN 2: MALDOC WEAPONIZATION, REGISTRY STAGING & LATERAL SPREAD (Target: Internal AD)
========================================================================================
[Malicious ZIP] ──► billy.tun opens Temp1_invoice.zip\invoice.doc via WINWORD.EXE (08:58:55 UTC)
       │
[Process Spawn] ──► WINWORD.EXE spawns encoded PowerShell (-NoP -sta -w 1 -enc WwBSAEU...)
       │
[Registry Staging] ──► Payload stored in HKCU:\Software\Microsoft\Windows Update\Update
                       & HKLM:\Software\Microsoft\Network\debug
       │
[Persistence] ──► schtasks.exe creates DAILY task "Updater" running as SYSTEM (09:15:03 UTC)
       │
[Lateral Movement] ──► Payload propagated to HKLM:\...\Network\debug across corporate fleet:
                       ├─► wrk-btun      (09:15:03 UTC)
                       ├─► wrk-klagerf   (09:34:26 UTC)
                       ├─► venus (DC)    (09:42:36 UTC - executed via FROTHLY\service3)
                       └─► mercury       (09:50:24 UTC)

| Tactic | Technique ID | Technique Name | Evidence & Forensics Observed |
| :--- | :--- | :--- | :--- |
| **Reconnaissance** | `T1595.002` | Vulnerability Scanning | Error-based SQL Injection using `updatexml()` against `/member.php`. |
| **Initial Access** | `T1566.001` | Spearphishing Attachment | Delivery of `invoice.zip` containing macro-enabled `invoice.doc`. |
| **Initial Access** | `T1566.002` | Spearphishing Link | Phishing email from `frankesters48@gmail.com` with malicious XSS URL. |
| **Execution** | `T1059.001` | PowerShell | Encoded PowerShell executing IEX from Registry keys. |
| **Execution** | `T1059.007` | JavaScript | Reflected XSS executing asynchronous DOM extraction in browser. |
| **Persistence** | `T1053.005` | Scheduled Task | `schtasks.exe /Create /RU system /TN Updater` running daily. |
| **Persistence** | `T1547.001` | Registry Run Keys / Startup | Payloads staged in `HKLM\Software\Microsoft\Network\debug`. |
| **Persistence** | `T1098` | Account Manipulation | Rogue admin creation `kIagerfield` (`usergroup=4`). |
| **Defense Evasion** | `T1027` | Obfuscated Files/Information | Base64-encoded command lines & hidden window execution flags (`-W hidden`). |
| **Defense Evasion** | `T1036.005` | Masquerading | Homograph attack: `kIagerfield` (capital `I` instead of lowercase `l`). |
| **Lateral Movement** | `T1021` | Remote Services | Propagation of malicious registry keys to `venus`, `mercury`, `wrk-klagerf`. |
| **Command & Control** | `T1573.002` | Asymmetric Encrypted C2 | Custom encrypted traffic over port 443 triggering Suricata TLS alerts. |

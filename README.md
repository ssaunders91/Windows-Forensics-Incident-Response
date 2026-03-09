# 🕵️ Windows Forensic Investigation: Post-Compromise Analysis

## 📌 Project Overview

This project focuses on host-based forensics on a compromised Windows workstation. Using a variety of native Windows tools and forensic utilities, I **analysed** the system to identify unauthorised accounts, persistence mechanisms, and evidence of "Living off the Land" (LotL) techniques used by an adversary.

---

## 🔍 Investigation Stages

### 1. Identifying Unauthorised Access

The first step was to identify how the attacker maintained access to the machine.

* **Observation:** I discovered a hidden local account that did not align with company naming conventions.
* **Analysis:** Using the `net user` command and **Local Users and Groups** manager, I identified the account `Guest1`, which had been elevated to the **Administrators** group.

### 2. Analysing Persistence Mechanisms

Attackers often use **Scheduled Tasks** or **Registry Run Keys** to ensure their malware survives a system reboot.

* **Detection:** I found a suspicious task named "CleanUp" that was configured to run a malicious script every time a user logged in.
* **Forensics:** I **analysed** the Task Scheduler logs and found that the task was executing a hidden file located in `C:\Windows\Temp\`.

### 3. Registry & Artefact Analysis

I used **Registry Editor** and **Eric Zimmerman's Tools** to find traces of executed programs.

* **Evidence:** By checking the **UserAssist** registry keys, I was able to prove that the attacker had executed `mimikatz.exe` to attempt credential dumping.
* **Timeline:** I reconstructed a timeline showing that the compromise occurred shortly after an RDP (Remote Desktop) session was established from an external IP.

---

## 🛠️ Tools & Techniques

* **Event Viewer:** **Analysed** Security Logs (Event ID 4624) to find successful logins.
* **Task Scheduler:** Investigated malicious persistence triggers.
* **Registry Editor (Regedit):** Examined `Run` and `RunOnce` keys for malicious entries.
* **Command Line (CMD/PowerShell):** Utilised `net user`, `netstat`, and `get-scheduledtask` for rapid triage.

---

## 🛡️ Mapping to MITRE ATT&CK

| Tactic | Technique | ID | Observation |
| --- | --- | --- | --- |
| **Persistence** | Create Account: Local Account | T1136.001 | Creation of "Guest1" administrative account. |
| **Persistence** | Scheduled Task/Job: Scheduled Task | T1053.005 | Malicious "CleanUp" task created for execution. |
| **Credential Access** | OS Credential Dumping | T1003 | Evidence of Mimikatz execution found in Registry. |
| **Lateral Movement** | Remote Services: Remote Desktop Protocol | T1021.001 | Unauthorised RDP session used for initial entry. |

---

## 📊 Technical Indicators (IOCs)

| Type | Value | Description |
| --- | --- | --- |
| **Account Name** | `Guest1` | Unauthorised Admin Account |
| **Task Name** | `CleanUp` | Persistence Mechanism |
| **File Path** | `C:\Windows\Temp\nc.exe` | Netcat binary used for a reverse shell |
| **Registry Key** | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` | Location of malicious startup entry |

---

## 💡 Key Takeaways & Remediation

1. **RDP Hardening:** Disable RDP on all workstations by default. If required, ensure it is only accessible via a VPN and protected by Multi-Factor Authentication (MFA).
2. **Principle of Least Privilege:** Audit the "Administrators" group regularly to ensure no unauthorised accounts have been added.
3. **Log Centralisation:** Forward Windows Event Logs to a central **SIEM** (like Splunk or ELK) to allow for real-time alerting on Event ID 4720 (Account Created).

---

*This investigation was performed as part of the TryHackMe SOC Level 1 path to demonstrate proficiency in Windows endpoint forensics.*

---


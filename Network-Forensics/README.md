# 🕵️ Network Forensics: Investigating Malicious Traffic with Wireshark

## 📌 Project Overview

This project involves a deep-dive forensic investigation of a PCAP (Packet Capture) file to identify signs of a network compromise. Using **Wireshark**, I **analysed** suspicious traffic patterns to uncover an **ICMP Tunnel** used for data exfiltration and a **Reverse Shell** established by an external adversary.

---

## 🔍 Investigation Stages

### 1. Identifying the ICMP Tunnel

During the initial triage, I observed an unusually high volume of ICMP (Ping) traffic.

* **Observation:** Standard pings usually contain arbitrary data. However, these packets contained encoded strings.
* **Analysis:** By inspecting the **Data** field of the ICMP requests, I found that the adversary was "tunnelling" commands and file contents inside ping packets to bypass the firewall.

### 2. Tracing the Reverse Shell

I filtered for TCP handshake attempts to identify unauthorised remote access.

* **Detection:** I identified a successful connection on **Port 4444** (a common default for Netcat).
* **Forensics:** By using the **"Follow TCP Stream"** feature, I reconstructed the attacker's session, revealing they had gained a command-line interface on the victim's machine.

### 3. Locating the Exfiltrated Data

The attacker used the established shell to locate a sensitive file named `secret.txt`.

* **Evidence:** I traced the file transfer back through the network logs, identifying the exact timestamp the data left the network via the covert ICMP channel.

---

## 🛠️ Tools & Techniques

* **Wireshark:** Primary tool for packet inspection and stream reconstruction.
* **Display Filters:** Used advanced filters (e.g., `icmp`, `tcp.port == 4444`, `http.request.method == "POST"`) to reduce noise and isolate threats.
* **Protocol Hierarchy:** **Analysed** the distribution of protocols to spot anomalies in non-standard ports.

---

## 🛡️ Mapping to MITRE ATT&CK

| Tactic | Technique | ID | Observation |
| --- | --- | --- | --- |
| **Command & Control** | Protocol Tunnelling | T1572 | ICMP packets used to encapsulate non-ping data. |
| **Command & Control** | Non-Application Layer Protocol | T1095 | Use of Port 4444 for raw TCP shell access. |
| **Exfiltration** | Exfiltration Over C2 Channel | T1041 | Sensitive files sent back through the established tunnel. |

---

## 📊 Indicators of Compromise (IOCs)

| Type | Value | Description |
| --- | --- | --- |
| **Source IP** | `10.10.x.x` | Internal Victim Machine |
| **Attacker IP** | `18.x.x.x` | External Command & Control Server |
| **Port** | `4444` | Unauthorised Reverse Shell Port |
| **Protocol** | `ICMP` | Misused for Data Tunnelling |

---

## 💡 Key Takeaways & Remediation

1. **ICMP Inspection:** Configure Firewalls/IDS to alert on ICMP packets with a payload size larger than the standard 64 bytes.
2. **Egress Filtering:** Implement "Least Privilege" for network traffic. Block all outbound traffic on non-standard ports (like 4444) unless explicitly required for business operations.
3. **Internal Monitoring:** Deploy an Intrusion Detection System (IDS) like **Snort** or **Suricata** to flag anomalous protocol **behaviour** automatically.

---
> **Analyst Note:** To isolate the malicious ICMP traffic during the investigation, I utilised the following Wireshark display filter to find packets with unusually large data payloads:

*This project was completed as part of the TryHackMe SOC Level 1 Path to demonstrate technical proficiency in network traffic analysis.*

[⬅️ Back to Profile Hub](https://www.google.com/search?q=https://github.com/ssaunders91)

-----

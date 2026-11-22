# 🔵 Blue Team Guide: Defensive Operations & Security Analysis

Defensive security professionals protect organizations against unauthorized access, detect anomalies across enterprise infrastructure, and contain security incidents.

---

## 🎯 Core Roles in Defense
- **Tier 1 / 2 SOC Analyst**: Triaging alerts, analyzing logs, validating true positives vs. false positives.
- **Incident Responder (IR)**: Containing active breaches, eradicating threats, and recovering compromised hosts.
- **Digital Forensics Specialist (DFIR)**: Deep-dive disk forensics, memory analysis, timeline reconstruction.
- **Threat Hunter**: Proactively searching for stealthy adversaries that bypassed automated detection.
- **Security Engineer**: Deploying and tuning SIEM, EDR, IDS/IPS, and firewalls.

---

## 🗺️ Step-by-Step Learning Progression

### Phase 1: Operating System & Host Internals
Understanding normal host behavior is required before identifying abnormal behavior:
- **Windows**:
  - Process relationships (`wininit.exe`, `services.exe`, `lsass.exe`, `svchost.exe`, `explorer.exe`).
  - Windows Event Log telemetry:
    - Event ID 4624 (Successful Logon)
    - Event ID 4625 (Failed Logon)
    - Event ID 4688 (Process Creation) / Sysmon Event ID 1
    - Event ID 7045 (New Service Installed)
  - Registry run keys and persistence locations.
- **Linux**:
  - System logs (`/var/log/auth.log`, `/var/log/syslog`, `/var/log/secure`).
  - Cron jobs (`/etc/cron*`, `/var/spool/cron/crontabs`).
  - Systemd service configurations and unit files.

### Phase 2: Network Telemetry & Packet Inspection
- **Packet Dissection**:
  - TCP handshake analysis (SYN, SYN-ACK, ACK), window size, TTL manipulation.
  - DNS tunneling and anomalous TXT record queries.
  - HTTP header inspection (user agents, URI anomalies, abnormal POST payloads).
- **Core Tools**:
  - [Wireshark](https://www.wireshark.org/) — Deep packet inspection.
  - [Zeek](https://zeek.org/) — Network security monitoring and connection state logs.
  - [Suricata / Snort](https://suricata.io/) — Signature-based Network Intrusion Detection (NIDS).

### Phase 3: SIEM & Log Aggregation
- **Splunk & Elastic (ELK) Stack**:
  - Ingesting syslog, Windows event logs, and web proxy feeds.
  - Crafting search queries, time-chart aggregations, and detection alerts.
  - Correlation rules: Detecting brute-force attempts followed by a successful login.
- **Wazuh (Open-Source EDR/SIEM)**:
  - Agent-based host monitoring, integrity checking, and compliance auditing.

### Phase 4: Active Directory Hardening & Hunting
- **Kerberos Attacks from a Defender's View**:
  - Kerberoasting: Detecting excessive TGS ticket requests with RC4 encryption (Event ID 4769).
  - AS-REP Roasting: Spotting accounts with "Do not require Kerberos preauthentication" enabled (Event ID 4768).
  - Pass-the-Hash / Overpass-the-Hash: Spotting NTLM logons across network boundaries (Event ID 4624 Type 3).
- **Hardening Strategies**:
  - Tiered Administrative Model (Tier 0 / 1 / 2 isolation).
  - Disabling SMBv1 and LLMNR/NBT-NS.
  - Enabling LAPS (Local Administrator Password Solution).

---

## 🧰 The Blue Team Toolkit
| Category | Free / Open-Source Tools | Purpose |
| :--- | :--- | :--- |
| **SIEM / Telemetry** | Wazuh, Splunk Free, Elastic Security | Centralized log analysis and alert correlation |
| **Endpoint Telemetry** | Microsoft Sysmon, Velociraptor | Detailed process creation, network connection logs |
| **Network Forensics** | Wireshark, Zeek, NetworkMiner | Packet analysis, credential extraction, artifact hunting |
| **Memory Forensics** | Volatility 3, MemProcFS | RAM extraction, rogue DLL and injected thread detection |
| **Malware Triage** | Any.Run, VirusTotal, Hybrid-Analysis, Ghidra | Sandboxing and static/dynamic binary analysis |

---

## 🎯 Recommended Next Steps
1. Complete the [THM: SOC Level 1](https://tryhackme.com/path/outline/soclevel1) track.
2. Solve 5 network forensic challenges on [CyberDefenders](https://cyberdefenders.org/).
3. Set up a local [Wazuh and Sysmon lab](../labs/home-lab-guide.md).

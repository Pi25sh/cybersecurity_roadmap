# ⚡ Cybersecurity Quick Reference & Essential Cheatsheets

A reference collection of essential commands and techniques across Linux, Windows, networking, and security auditing.

---

## 🐧 Linux Command Line Essentials

### User & Permission Auditing
```bash
# Check current user, groups, and privileges
id
whoami
sudo -l

# Search for SUID binaries (potential privilege escalation vectors)
find / -perm -4000 -type f 2>/dev/null

# Inspect world-writable directories and files
find / -writable -type d 2>/dev/null
```

### Network & Listening Ports
```bash
# Inspect all listening TCP and UDP sockets with process names
ss -tulpn
netstat -tulnp

# Monitor established network connections in real-time
ss -ta
```

### System Logs & Triage
```bash
# Follow authentication logs
tail -f /var/log/auth.log       # Debian / Ubuntu
tail -f /var/log/secure         # RHEL / CentOS / Rocky

# Query systemd journal for specific service failures
journalctl -u ssh -n 50 --no-pager
```

---

## 🪟 Windows & PowerShell Essentials

### System & User Discovery
```powershell
# Basic host identity and domain status
hostname
whoami /all
[System.Security.Principal.WindowsIdentity]::GetCurrent().Claims

# List local administrators
net localgroup administrators

# Query active domain controllers (when domain joined)
nltest /dclist:
```

### Process & Service Auditing
```powershell
# List running processes with executable paths
Get-Process | Select-Object Id, ProcessName, Path

# Query services with unquoted paths (space in path without quotes)
Get-WmiObject win32_service | Where-Object { $_.PathName -notmatch '^"' -and $_.PathName -match ' ' } | Select-Object Name, PathName, StartMode
```

### Network & Port Enumeration
```powershell
# Active TCP connections and owning process IDs
Get-NetTCPConnection -State Listen | Select-Object LocalAddress, LocalPort, OwningProcess

# Test port reachability without Telnet
Test-NetConnection -ComputerName 192.168.1.1 -Port 445
```

---

## 🌐 Network Analysis & Packet Crafting

### Nmap Scanning Profiles
```bash
# Fast SYN stealth scan without ping resolution
nmap -sS -Pn -T4 -p- <target_ip> -oN full_port_scan.txt

# Service version detection with default safe scripts
nmap -sV -sC -p 21,22,80,443,445,3389 <target_ip> -oA service_audit

# UDP top ports audit
nmap -sU --top-ports 50 <target_ip>
```

### Tcpdump Quick Filters
```bash
# Capture HTTP traffic excluding SSH noise
tcpdump -i eth0 -nn -s0 -A 'tcp port 80 and not port 22'

# Capture DNS requests and responses
tcpdump -i eth0 -nn -s0 'udp port 53'
```

---

## 🔑 Active Directory Reference

### Essential AD Event IDs (Defensive Telemetry)
| Event ID | Provider | Description |
| :--- | :--- | :--- |
| **4624** | Security | Successful account logon |
| **4625** | Security | Failed account logon (potential brute force) |
| **4672** | Security | Special privileges assigned to new logon (Admin rights) |
| **4688** | Security | New process created (requires command line auditing) |
| **4768** | Security | Kerberos TGT requested (Authentication ticket) |
| **4769** | Security | Kerberos Service Ticket (TGS) requested (Kerberoasting indicator) |
| **7045** | System | New service was installed in the system |

### Common Attack Vector Mapping
| Attack Technique | MITRE ID | Target / Mechanism | Primary Detection |
| :--- | :--- | :--- | :--- |
| **Kerberoasting** | T1558.003 | Service account TGS requests | High volume of Event ID 4769 with RC4 ticket encryption |
| **AS-REP Roasting** | T1558.004 | Accounts without pre-auth | Event ID 4768 with pre-auth type `0` |
| **Pass-the-Hash** | T1550.002 | NTLM hash reuse | Event ID 4624 Logon Type 3 with NTLM authentication package |
| **DCSync** | T1003.006 | Directory Replication Service | Replicating directory changes permissions (`DS-Replication-Get-Changes`) from non-DC IP |

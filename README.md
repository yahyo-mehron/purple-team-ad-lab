# Purple Team Active Directory Lab

Hands-on Purple Team / Detection Engineering lab focused on adversary simulation, Windows telemetry, Sysmon, Wazuh SIEM, custom detections, built-in correlation validation, MITRE ATT&CK mapping, investigation, remediation and retesting.

This is a personal lab/portfolio project, not a claim of commercial SOC experience.

## Goals

- Build an Active Directory environment
- Collect Windows security telemetry
- Simulate attacker techniques
- Map attacks to MITRE ATT&CK
- Develop detection rules
- Investigate generated alerts
- Document detection gaps
- Retest detections

## Lab Architecture

| Host | Operating System | Purpose | Network |
|------|------------------|---------|---------|
| DC01 | Windows Server 2025 | AD DS + DNS | `192.168.226.10` (VMware NAT) |
| WS01 | Windows 11 Pro | Domain workstation / telemetry | `192.168.226.129` (VMware NAT + Tailscale) |
| ATTACK01 | Kali Linux | Adversary simulation | `192.168.226.132` (VMware NAT) |
| SIEM01 | Ubuntu Server 24.04 | Wazuh all-in-one (Beget VPS) | Tailscale `100.64.245.1` |

Domain: `ad.purple.test` (NetBIOS: `PURPLE`)

```text
ATTACK01 ----\
              \
DC01 ---------- local VMware network (192.168.226.0/24)
              /
WS01 --------/
  |
  | Tailscale
  v
SIEM01 (Beget VPS / Ubuntu / Wazuh)
```

## Current Detection Pipeline

```text
ATTACK01 / local activity
   ↓
WS01 (Sysmon + Windows Security)
   ↓
Wazuh Agent
   ↓
Tailscale
   ↓
SIEM01 (Wazuh Manager / Indexer / Dashboard)
   ↓
Detection / Alert
```

## Completed Detection Scenarios

| # | Scenario | Telemetry | Detection | Rule(s) | MITRE | Status |
|---|----------|-----------|-----------|---------|-------|--------|
| 1 | [RPC Reconnaissance](docs/detections/rpc-reconnaissance.md) | Sysmon EID 3 + Wireshark | Custom Wazuh | `100100` | T1046 | Completed |
| 2 | [PowerShell ExecutionPolicy Bypass](docs/detections/powershell-executionpolicy-bypass.md) | Sysmon EID 1 | Built-in + custom | `92027` → `100101` | T1059.001 | Completed |
| 3 | [Password Guessing](docs/detections/password-guessing.md) | Windows 4625 | Built-in correlation | `60122` → `60204` | T1110 | Completed |

Notes:

- RPC custom rule `100100` detects inbound TCP/135 from ATTACK01; Wireshark confirms EPM Lookup semantics
- PowerShell includes a portable Sigma equivalent under `detections/sigma/`
- Password guessing uses built-in Wazuh correlation (`60204`), not a custom rule

## Current Progress

- [x] Windows Server installed
- [x] DC01 configured
- [x] Active Directory deployed
- [x] Domain created: ad.purple.test
- [x] Organizational Units created
- [x] Test users created
- [x] Security groups created
- [x] Domain admin account created
- [x] Windows 11 workstation deployed
- [x] WS01 joined to ad.purple.test
- [x] Domain logon verified with alice
- [x] WS01 moved to Workstations OU
- [x] Sysmon installed on WS01
- [x] Sysmon Operational log verified
- [x] Process creation telemetry tested (Event ID 1)
- [x] Wazuh SIEM deployed on SIEM01
- [x] WS01 enrolled as Wazuh agent
- [x] Sysmon Operational log forwarded to Wazuh
- [x] Sysmon Event ID 1 telemetry visible in SIEM
- [x] Kali ATTACK01 deployed
- [x] Sysmon Event ID 3 network telemetry enabled
- [x] RPC Endpoint Mapper reconnaissance simulated
- [x] Wireshark RPC traffic validated
- [x] Custom Wazuh detection rule 100100 created
- [x] RPC reconnaissance detected in Wazuh
- [x] First Attack → Telemetry → Detection → Alert scenario completed
- [x] MITRE ATT&CK T1046 mapped
- [x] SIEM01 migrated to Beget VPS (Tailscale access)
- [x] Suspicious PowerShell ExecutionPolicy Bypass detected (`92027` + `100101`)
- [x] Sigma rule for PowerShell ExecutionPolicy Bypass added
- [x] Password guessing validated via built-in correlation (`60204` / T1110)

![AD structure](screenshots/03-ad-structure.png)

## Technologies

- Active Directory
- Windows Server / Windows 11
- Sysmon
- Wazuh
- Kali Linux
- Impacket
- Wireshark
- SMB / NTLM
- PowerShell
- Sigma
- MITRE ATT&CK
- Tailscale
- Linux / Ubuntu
- Git / GitHub

## Documentation

- [Active Directory Setup](docs/active-directory-setup.md)
- [Windows Workstation Setup](docs/workstation-setup.md)
- [Sysmon Setup](docs/sysmon-setup.md)
- [Wazuh Setup](docs/wazuh-setup.md)
- [RPC Reconnaissance Detection Report](docs/detections/rpc-reconnaissance.md)
- [PowerShell ExecutionPolicy Bypass](docs/detections/powershell-executionpolicy-bypass.md)
- [Password Guessing](docs/detections/password-guessing.md)
- [Detection Rules Index](detections/README.md)

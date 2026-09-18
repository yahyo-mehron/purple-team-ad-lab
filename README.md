# Purple Team Active Directory Lab

This project documents the creation of a Purple Team laboratory
for adversary emulation, detection engineering and incident investigation.

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

| Host | Operating System | Purpose |
|------|------------------|---------|
| DC01 | Windows Server 2025 | Active Directory Domain Controller |
| WS01 | Windows 11 Pro | Domain workstation |
| ATTACK01 | Kali Linux | Adversary simulation |
| SIEM01 | Ubuntu Server 24.04 | Wazuh SIEM |

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

![AD structure](screenshots/03-ad-structure.png)

## Current Detection Pipeline

```text
ATTACK01
   ↓
WS01
   ↓
Sysmon
   ↓
Wazuh Agent
   ↓
SIEM01
   ↓
Wazuh Detection / Alert
```

## Detection Scenarios

- [RPC Endpoint Mapper Reconnaissance](docs/detections/rpc-reconnaissance.md) — ATTACK01 → WS01 → Sysmon Event ID 3 → Wazuh rule 100100 → MITRE T1046

## Documentation

- [Active Directory Setup](docs/active-directory-setup.md)
- [Windows Workstation Setup](docs/workstation-setup.md)
- [Sysmon Setup](docs/sysmon-setup.md)
- [Wazuh Setup](docs/wazuh-setup.md)
- [RPC Reconnaissance Detection Report](docs/detections/rpc-reconnaissance.md)

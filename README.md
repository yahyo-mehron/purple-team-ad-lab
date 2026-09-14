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
| DC01 | Windows Server | Active Directory Domain Controller |
| WS01 | Windows 11 | Domain workstation |
| ATTACK01 | Kali Linux | Adversary simulation |
| SIEM01 | Ubuntu Linux | Wazuh SIEM |

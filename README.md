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

## Future Architecture

```
ATTACK01
Kali Linux
    |
    v
WS01
Windows 11
    |
    v
DC01
Windows Server / Active Directory
    |
    v
SIEM01
Wazuh
```

| Host | Role | OS / Stack |
|------|------|------------|
| ATTACK01 | Attacker / adversary emulation | Kali Linux |
| WS01 | Domain-joined workstation | Windows 11 |
| DC01 | Domain Controller | Windows Server / Active Directory |
| SIEM01 | Security monitoring & detection | Wazuh |

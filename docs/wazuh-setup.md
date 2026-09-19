# Wazuh Setup

## Overview

Wazuh is the SIEM platform for the Purple Team lab. It collects, normalizes and alerts on endpoint telemetry from the domain workstation `WS01`.

| Component | Value |
|---|---|
| Host | SIEM01 |
| Current hosting | Beget VPS |
| Operating System | Ubuntu Server 24.04 |
| Hostname | `siem01` |
| Deployment | Wazuh all-in-one |
| Resources | 4 vCPU / ~6 GB RAM / ~80 GB disk |
| Access path | Tailscale |
| Tailscale IP | `100.64.245.1` |
| Agent | WS01 |
| Domain | ad.purple.test |

## Current Architecture

```text
ATTACK01 / DC01 / WS01
        (local VMware 192.168.226.0/24)
WS01
  │
  │ Tailscale
  ▼
SIEM01 (Beget VPS)
  ├── Wazuh Manager
  ├── Wazuh Indexer
  └── Wazuh Dashboard
```

Security notes:

- Dashboard access is performed through Tailscale
- WS01 Wazuh Agent reaches SIEM01 through the Tailscale overlay
- Wazuh management/dashboard services are not intentionally exposed broadly to the public Internet

## Migration

### Old (local VMware SIEM)

```text
WS01 → 192.168.226.20 → local Wazuh (SIEM01 VM)
```

### Current (Beget VPS)

```text
WS01 → Tailscale → 100.64.245.1 → Beget SIEM01
```

The previous local SIEM01 address was `192.168.226.20`. Historical detection evidence from earlier lab stages may still reference that address.

## Agent Enrollment

The domain workstation `WS01` is enrolled as a Wazuh agent and connected successfully to the manager on SIEM01.

## Sysmon Log Collection

The following Windows event channel is forwarded from WS01 to Wazuh:

`Microsoft-Windows-Sysmon/Operational`

## Verification

Validated telemetry includes:

- Sysmon Event ID 1 — Process Create
- Sysmon Event ID 3 — Network Connection
- Windows Security authentication failure events (`4625`)

Evidence from early SIEM validation:

![Wazuh Sysmon telemetry](../screenshots/07-wazuh-sysmon-events.png)

## Detection Validation

Completed detection scenarios:

| Scenario | Telemetry | Detection type | Rule(s) | MITRE |
|----------|-----------|----------------|---------|-------|
| [RPC reconnaissance](detections/rpc-reconnaissance.md) | Sysmon EID 3 | Custom | `100100` | T1046 |
| [PowerShell ExecutionPolicy Bypass](detections/powershell-executionpolicy-bypass.md) | Sysmon EID 1 | Built-in + custom | `92027` → `100101` | T1059.001 |
| [Password guessing](detections/password-guessing.md) | Windows `4625` | Built-in correlation | `60122` → `60204` | T1110 |

RPC evidence screenshot:

![RPC recon detection details](../screenshots/08-rpc-recon-detection-details.png)

## Troubleshooting

### Wrong manager IP during early agent deployment

During early agent deployment on the local SIEM, WS01 initially connected to an incorrect manager address:

`192.162.226.20`

The issue was identified in:

`C:\Program Files (x86)\ossec-agent\ossec.log`

and corrected in:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

to the then-local manager address `192.168.226.20`, followed by:

```powershell
Restart-Service wazuhsvc
```

### Previous local SIEM disk / LVM incident

This incident relates to the **previous local SIEM VM**, not the current Beget VPS.

Symptoms:

- root filesystem filled
- Wazuh Indexer / Dashboard failures
- VMware disk ~60 GB, but Ubuntu root LVM initially remained ~29 GB

Fix used on the local SIEM VM:

```bash
sudo apt clean
sudo lvextend -l +100%FREE -r /dev/ubuntu-vg/ubuntu-lv
df -h /
```

Lesson: expanding a VMware virtual disk does not automatically grow an LVM root filesystem.

## Result

Wazuh is used for both:

- custom detection engineering (`100100`, `100101`)
- validation of built-in correlation detections (`60204`)

## Next Steps

- Tune RPC reconnaissance rule v2 (remove hardcoded attacker IP)
- Improve PowerShell bypass false-positive handling
- Add additional Sigma equivalents
- Expand authentication-attack coverage beyond single-account guessing

# Wazuh Setup

## Overview

Wazuh was deployed as the SIEM platform for the Purple Team lab to collect, normalize and review endpoint telemetry from the domain workstation `WS01`.

| Component | Value |
|---|---|
| Host | SIEM01 |
| IP address | `192.168.226.20` |
| Operating System | Ubuntu Server 24.04 |
| Deployment | Wazuh all-in-one |
| Agent | WS01 |
| Domain | ad.purple.test |

## SIEM Host

SIEM01 runs Ubuntu Server 24.04 and hosts a Wazuh all-in-one installation, including the manager, indexer and dashboard components on a single host.

## Agent Enrollment

The domain workstation `WS01` was enrolled as a Wazuh agent and connected successfully to the manager on SIEM01.

## Architecture

```text
WS01
  │
  │ Wazuh Agent
  │ Sysmon telemetry
  ▼
SIEM01
  ├── Wazuh Manager
  ├── Wazuh Indexer
  └── Wazuh Dashboard
```

## Sysmon Log Collection

The following Windows event channel is forwarded from WS01 to Wazuh:

`Microsoft-Windows-Sysmon/Operational`

## Verification

Sysmon Event ID 1 telemetry from WS01 is visible in the Wazuh Dashboard.

This confirms that:

- the Wazuh agent on WS01 is connected
- the Sysmon Operational channel is being collected
- process creation events are reaching the SIEM

The following telemetry was verified in the dashboard:

- Sysmon Event ID 1 — Process Create
- Process image and command line
- Parent process information
- User context
- Process hashes

## Evidence

![Wazuh Sysmon telemetry](../screenshots/07-wazuh-sysmon-events.png)

## Troubleshooting

During agent deployment, WS01 initially connected to an incorrect manager address:

`192.162.226.20`

The issue was identified through the Wazuh agent log and corrected in:

`C:\Program Files (x86)\ossec-agent\ossec.conf`

The manager address was changed to:

`192.168.226.20`

The Wazuh agent service was then restarted successfully.

## Result

Endpoint telemetry from WS01 is available in Wazuh and can be used for detection engineering, threat hunting and future adversary simulation exercises.

## Next Steps

- Review additional Sysmon event types in Wazuh
- Deploy a custom Sysmon configuration
- Create detection rules for simulated attacks
- Perform the first MITRE ATT&CK simulation

# Active Directory Lab Setup

## Overview

This document describes the initial Active Directory configuration used in the Purple Team lab.

The environment is designed for:

- adversary emulation
- detection engineering
- Windows telemetry collection
- Active Directory attack simulation
- incident investigation

## Domain

- Domain: `ad.purple.test`
- Domain Controller: `DC01`
- Operating System: `Windows Server 2025`
- IP address: `192.168.226.10`

## Network Configuration

- Hostname: `DC01`
- IPv4: `192.168.226.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `192.168.226.2`
- DNS: `192.168.226.10`

## Active Directory Configuration

The following roles were installed:

- Active Directory Domain Services
- DNS Server

A new Active Directory forest was created:

`ad.purple.test`

The server was promoted to the first Domain Controller in the forest.

## Organizational Units

```text
PurpleLab
├── Admins
├── Groups
├── Servers
├── Users
└── Workstations
```

## Test Users

| User | Role |
|------|------|
| alice | Standard user |
| bob | Standard user |
| helpdesk | Helpdesk user |
| itadmin | Privileged administrator |

## Security Groups

- `GG-Employees`
- `GG-Helpdesk`

## Privileged Account

The `itadmin` account was added to the Domain Admins group.

## Next Steps

- Deploy Windows 11 workstation `WS01`
- Join `WS01` to `ad.purple.test`
- Configure Windows event logging
- Install Sysmon
- Deploy Wazuh
- Perform first MITRE ATT&CK simulation

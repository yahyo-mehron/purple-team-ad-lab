# Windows Workstation Setup

## Overview

This document describes the deployment and domain configuration of the Windows 11 workstation used in the Purple Team lab.

## Workstation

- Hostname: `WS01`
- Operating System: `Windows 11 Pro`
- Purpose: Domain workstation for user activity, telemetry collection and attack simulation

## Network Configuration

The workstation receives its IP address through DHCP.

DNS was manually configured to use the Domain Controller:

- DNS Server: `192.168.226.10`

This allows the workstation to resolve the Active Directory domain and Domain Controller.

## Connectivity Tests

The following checks were performed successfully:

```text
ping 192.168.226.10
nslookup ad.purple.test
ping dc01.ad.purple.test
```

The workstation was able to resolve:

- `ad.purple.test`
- `dc01.ad.purple.test`

to:

`192.168.226.10`

## Domain Join

The workstation was joined to:

`ad.purple.test`

Administrative credentials from the domain were used to complete the join.

## Domain User

The workstation is used by the standard domain account:

`PURPLE\alice`

## Active Directory Placement

The `WS01` computer object was moved to:

```text
PurpleLab
└── Workstations
```

## Next Steps

- Install Sysmon
- Configure Windows event logging
- Forward telemetry to the SIEM

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

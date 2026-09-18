# Sysmon Setup

## Overview

Sysmon was deployed on the domain workstation `WS01` to provide enhanced endpoint telemetry for the Purple Team lab.

| Component | Value |
|---|---|
| Host | WS01 |
| OS | Windows 11 Pro |
| Domain | ad.purple.test |
| Sysmon | Microsoft Sysinternals Sysmon |
| Log | Microsoft-Windows-Sysmon/Operational |

## Installation

Sysmon was installed from:

```powershell
C:\Tools\Sysmon
```

Installation command:

```powershell
.\Sysmon64.exe -accepteula -i
```

## Verification

The Sysmon service was verified with:

```powershell
Get-Service *sysmon*
```

The service was running successfully.

The Sysmon event log was verified with:

```powershell
Get-WinEvent -ListLog *Sysmon*
```

Log:

`Microsoft-Windows-Sysmon/Operational`

## Process Creation Test

A test process was started:

`notepad.exe`

Sysmon successfully recorded the process creation as:

- Event ID: `1`
- Event Type: Process Create
- Image: `C:\Windows\System32\notepad.exe`
- ParentImage: `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`

The event also contained:

- Process GUID
- Process ID
- Command line
- User
- Integrity level
- SHA256 hash
- Parent process information

## Network Connection Telemetry

Sysmon configuration was later updated to collect network connection telemetry.

Confirmed:

- Event ID: `3` — Network Connection
- inbound RPC connection from ATTACK01
- source IP: `192.168.226.132`
- destination IP: `192.168.226.129`
- destination port: `135`
- initiated: `false`

This telemetry was later used by Wazuh rule `100100` to detect RPC reconnaissance.

## Current Verified Event IDs

| Event ID | Type | Status |
|----------|------|--------|
| 1 | Process Create | Verified |
| 3 | Network Connection | Verified |

## Result

Sysmon telemetry collection on WS01 is operational.

Verified process creation and network connection telemetry are available for detection engineering and attack simulation exercises.

## Next Steps

- Expand Sysmon configuration
- Review DNS Query telemetry
- Review registry telemetry
- Review process access telemetry
- Reduce noisy events
- Develop additional detection scenarios

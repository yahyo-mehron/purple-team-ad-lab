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

## Result

Sysmon telemetry collection on WS01 is operational.

The workstation can now provide enhanced Windows telemetry for future detection engineering and attack simulation exercises.

## Next Steps

- Test process creation from a standard domain user
- Deploy a custom Sysmon configuration
- Review additional Sysmon event types
- Forward Sysmon logs to the SIEM
- Create detection rules for simulated attacks

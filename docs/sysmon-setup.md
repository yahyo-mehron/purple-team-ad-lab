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

Expected: `Running`

The Sysmon event log was verified with:

```powershell
Get-WinEvent -ListLog *Sysmon*
```

Log channel:

`Microsoft-Windows-Sysmon/Operational`

## Custom Configuration

Path:

`C:\Tools\Sysmon\sysmonconfig.xml`

Relevant configuration used in the lab:

```xml
<Sysmon schemaversion="4.91">
  <HashAlgorithms>sha256</HashAlgorithms>
  <EventFiltering>
    <ProcessCreate onmatch="exclude" />
    <NetworkConnect onmatch="exclude" />
    <DnsQuery onmatch="exclude" />
  </EventFiltering>
</Sysmon>
```

Applied with:

```powershell
.\Sysmon64.exe -c .\sysmonconfig.xml
```

An empty `onmatch="exclude"` block excludes nothing, so these event classes are collected.

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

Later, suspicious PowerShell execution with `ExecutionPolicy Bypass` was also observed via Event ID 1 and used by Wazuh rules `92027` / `100101`.

## Network Connection Telemetry

Sysmon configuration was updated to collect network connection telemetry.

Confirmed:

- Event ID: `3` — Network Connection
- inbound RPC connection from ATTACK01
- source IP: `192.168.226.132`
- destination IP: `192.168.226.129`
- destination port: `135`
- initiated: `false`

This telemetry was later used by custom Wazuh rule `100100` to detect RPC reconnaissance network activity.

## Current Verified Event IDs

| Event ID | Type | Status |
|----------|------|--------|
| 1 | Process Create | Verified |
| 3 | Network Connection | Verified |

Event ID 22 (DNS Query) is configured for collection in the current config but is **not** claimed as verified here.

## Result

Sysmon telemetry collection on WS01 is operational and supports both process-creation and network-connection detection engineering.

## Next Steps

- Expand Sysmon configuration
- Review DNS Query telemetry
- Review registry telemetry
- Review process access telemetry
- Reduce noisy events
- Develop additional detection scenarios

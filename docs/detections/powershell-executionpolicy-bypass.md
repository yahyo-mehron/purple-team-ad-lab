# Detection Report: PowerShell ExecutionPolicy Bypass

## Overview

This scenario validates detection of suspicious PowerShell execution that uses `-ExecutionPolicy Bypass` on the domain workstation `WS01`.

The exercise confirmed that Sysmon process-creation telemetry can be correlated with a built-in Wazuh rule and further refined by a custom command-line detection rule.

## Objective

- Generate PowerShell process-creation telemetry (Sysmon Event ID 1)
- Observe built-in Wazuh detection for nested PowerShell spawning
- Validate a custom Wazuh rule that inspects `ExecutionPolicy Bypass`
- Map the activity to MITRE ATT&CK T1059.001

## Environment

| Host | Role | IP / Identity | Notes |
|------|------|---------------|-------|
| WS01 | Target / telemetry source | `192.168.226.129` | Domain workstation, Sysmon + Wazuh agent |
| SIEM01 | Wazuh all-in-one | Tailscale `100.64.245.1` | Detection / alerting |
| Account | Executor | `PURPLE\itadmin` | Elevated PowerShell, IntegrityLevel High |

Domain: `ad.purple.test`

## Test Command

Executed on WS01:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -Command "Get-Process | Select-Object -First 5"
```

Context:

- User: `PURPLE\itadmin`
- Elevated PowerShell
- IntegrityLevel: High

## Telemetry

Sysmon recorded the activity as Event ID `1` (Process Create).

Observed fields:

| Field | Value |
|-------|-------|
| Image | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` |
| CommandLine | contains `-NoProfile -ExecutionPolicy Bypass` |
| ParentImage | `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe` |
| User | `PURPLE\itadmin` |
| IntegrityLevel | High |
| Hashes | SHA256 present |

## Wazuh Built-in Detection

Wazuh first matched a built-in rule:

| Field | Value |
|-------|-------|
| Rule ID | `92027` |
| Level | `4` |
| Description | Powershell process spawned powershell instance |
| MITRE | T1059.001 PowerShell |
| Groups | `sysmon`, `sysmon_eid1_detections`, `windows` |

This built-in rule indicates nested PowerShell process creation. It does **not** by itself prove that `ExecutionPolicy Bypass` was used.

## Custom Rule

A custom rule was created to inspect the command line after the built-in match:

- Rule ID: `100101`
- Level: `8`
- Description: Suspicious PowerShell execution with ExecutionPolicy Bypass
- Rule file: [powershell-executionpolicy-bypass-100101.xml](../../detections/wazuh/powershell-executionpolicy-bypass-100101.xml)

```xml
<group name="windows,sysmon,powershell,">

  <rule id="100101" level="8">
    <if_sid>92027</if_sid>

    <field name="win.eventdata.commandLine" type="pcre2">(?i)ExecutionPolicy\s+Bypass</field>

    <description>Suspicious PowerShell execution with ExecutionPolicy Bypass</description>

    <mitre>
      <id>T1059.001</id>
    </mitre>

    <group>powershell,execution_policy_bypass,suspicious_execution,</group>
  </rule>

</group>
```

## Detection Logic

```text
PowerShell execution
→ Sysmon Event ID 1
→ Wazuh built-in rule 92027
→ command line inspection
→ custom rule 100101
→ level 8
→ MITRE T1059.001
```

Important:

- Rule `92027` = built-in nested PowerShell detection
- Rule `100101` = custom command-line refinement for `ExecutionPolicy Bypass`

## MITRE ATT&CK Mapping

| Field | Value |
|-------|-------|
| Tactic | Execution |
| Technique | Command and Scripting Interpreter: PowerShell |
| Technique ID | [T1059.001](https://attack.mitre.org/techniques/T1059/001/) |

## Investigation

When reviewing an alert from rule `100101`, investigate:

- full command line
- parent process
- user and integrity level
- whether the activity was expected administrative work
- whether additional suspicious PowerShell activity followed
- host context (`WS01`) and account (`PURPLE\itadmin`)

## False Positives

`ExecutionPolicy Bypass` is not malicious by itself.

Legitimate administrative, deployment, packaging, and automation scripts may use it.

Treat this detection as **suspicious execution requiring context**, not as confirmed malware.

## Remediation / Response Ideas

- Confirm whether the parent process / operator intended the command
- Review related process-creation events around the same timestamp
- Restrict unnecessary use of `ExecutionPolicy Bypass` where possible
- Prefer signed / constrained PowerShell practices in production environments
- Tune allowlists carefully for known admin tooling

## Retest

Re-running the test command regenerated the expected detection chain:

1. Sysmon Event ID 1
2. Built-in rule `92027`
3. Custom rule `100101` (level 8)

## Lessons Learned

- Built-in rules are useful starting points and can be refined with custom logic
- Command-line inspection provides the semantic detail that process-spawn alone lacks
- High integrity / privileged context increases investigative priority
- Portable Sigma logic can document the same idea outside Wazuh-specific XML

Sigma equivalent (not claimed as deployed):

[powershell-executionpolicy-bypass.yml](../../detections/sigma/powershell-executionpolicy-bypass.yml)

## Status

**Completed**

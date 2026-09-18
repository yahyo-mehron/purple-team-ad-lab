# Detection Report: RPC Reconnaissance

## Objective

Detect inbound RPC Endpoint Mapper reconnaissance against the domain workstation `WS01` and validate that Sysmon network telemetry can be turned into a useful SIEM alert.

This exercise covers the first Attack → Telemetry → Detection → Alert loop in the Purple Team lab.

## Lab Hosts

| Host | Role | IP | Notes |
|------|------|----|-------|
| WS01 | Target / Wazuh agent | `192.168.226.129` | Domain workstation, Sysmon enabled |
| SIEM01 | Wazuh all-in-one | `192.168.226.20` | Manager / Indexer / Dashboard |
| Source host | Attacker / recon source | `192.168.226.132` | Generated inbound TCP/135 traffic to WS01 |
| DC01 | Domain Controller | `192.168.226.10` | Not the detection target in this scenario |

Domain: `ad.purple.test`

## Attack Command

RPC Endpoint Mapper reconnaissance was performed against WS01 on TCP port `135`.

Representative attack activity from the source host:

```bash
nmap -sV -p 135 192.168.226.129
```

Purpose:

- probe the RPC Endpoint Mapper service
- generate inbound network telemetry on WS01
- validate detection coverage for early recon behavior

Run on: attacker / recon source host (`192.168.226.132`)

## Wireshark Evidence

Packet capture on the lab network showed TCP/RPC traffic from `192.168.226.132` to WS01 (`192.168.226.129:135`):

- TCP three-way handshake to destination port `135`
- `DCERPC Bind` to Endpoint Mapper (`EPMv4`)
- `EPM Lookup request` (core reconnaissance action)
- Fragmented `DCERPC Response` packets returning registered RPC endpoints
- TCP `FIN, ACK` closing the session

This confirms Endpoint Mapper enumeration at the network layer, before higher-level AD abuse or lateral movement.

![RPC recon Wireshark](../../screenshots/07-rpc-recon-wireshark.png)

## Sysmon Event ID 3

Sysmon recorded the network connection as:

- Event ID: `3`
- Event Type: Network connection
- Destination port: `135`
- Destination port name: `epmap`
- Protocol: TCP
- Image: `C:\Windows\System32\svchost.exe`
- User: `NT AUTHORITY\NETWORK SERVICE`
- Initiated: `false` (inbound connection)

Observed source/destination pair:

- Source IP: `192.168.226.132`
- Destination IP: `192.168.226.129`

This is valuable because inbound RPC mapping activity is visible even when the listening process is a legitimate system service.

## Wazuh Rule 100100

A custom Wazuh detection rule was created:

- Rule ID: `100100`
- Purpose: alert on Sysmon network connection telemetry related to RPC Endpoint Mapper recon against WS01

The rule consumes Sysmon Event ID 3 fields such as:

- destination port `135`
- destination IP / agent context
- source IP
- process image / user context when available

## Alert Evidence

The alert was verified in Wazuh Dashboard with query:

```text
rule.id:100100
```

Result:

- Hits: `2`
- Agent name: `WS01`
- Agent ID: `001`
- Agent IP: `192.168.226.129`
- Source IP: `192.168.226.132`
- Destination port: `135` (`epmap`)
- Protocol: TCP
- Image: `C:\Windows\System32\svchost.exe`
- User: `NT AUTHORITY\NETWORK SERVICE`

Evidence screenshot:

![RPC recon detection details](../../screenshots/08-rpc-recon-detection-details.png)

## Limitations / False Positives

This detection is useful for lab visibility, but has practical limitations:

- Legitimate Windows / AD management traffic may also use RPC Endpoint Mapper (`TCP/135`)
- Alerting only on destination port `135` can produce noise in denser environments
- `svchost.exe` under `NETWORK SERVICE` is expected for many normal RPC services
- Source allowlisting / subnet context is needed before promoting this as a high-fidelity production rule
- The current rule proves telemetry and alerting work; it is not yet a complete hunting playbook

Possible tuning ideas:

- exclude known admin / jump hosts
- correlate with unusual external source subnets
- require burst / scan patterns instead of a single connection
- enrich with follow-on RPC interface enumeration activity

## MITRE ATT&CK Mapping

| Field | Value |
|-------|-------|
| Tactic | Discovery |
| Technique | Network Service Discovery |
| Technique ID | [T1046](https://attack.mitre.org/techniques/T1046/) |
| Related activity | RPC Endpoint Mapper reconnaissance over TCP/135 |

This maps cleanly to early-stage adversary recon before credential access or lateral movement.

## Lessons Learned

- Sysmon Event ID 3 is enough to detect inbound RPC recon when forwarded to Wazuh
- Custom rule `100100` successfully closed the Attack → Detect loop for this scenario
- Detection quality depends on context: port `135` alone is weak without source/baseline tuning
- Evidence collection should include packet capture, endpoint telemetry and SIEM alert screenshots together
- Next improvements: reduce false positives, document exact Sigma/Wazuh rule logic in-repo, and expand to follow-on RPC enumeration techniques

## Status

**Done** — first detection engineering scenario validated end-to-end in the lab.

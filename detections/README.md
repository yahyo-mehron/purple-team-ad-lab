# Detection Rules

This directory contains detection logic developed and validated in the Purple Team lab.

## Wazuh

| Rule | Description | MITRE ATT&CK | Status |
|------|-------------|--------------|--------|
| 100100 | RPC reconnaissance from ATTACK01 to TCP/135 | T1046 Network Service Discovery | Validated |

Rule file:

- [wazuh/rpc-recon-100100.xml](wazuh/rpc-recon-100100.xml)

Detection report:

- [RPC Reconnaissance Detection Report](../docs/detections/rpc-reconnaissance.md)

> Current rule `100100` is lab-specific and uses a hardcoded source IP.
> A future rule v2 should remove this dependency and introduce behavioral / frequency-based tuning.

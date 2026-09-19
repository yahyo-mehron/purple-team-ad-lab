# Detection Rules

This directory contains detection logic developed and validated in the Purple Team lab.

## Scenario Index

| Scenario | Source | Detection | Rule | MITRE | Status |
|----------|--------|-----------|------|-------|--------|
| [RPC reconnaissance](../docs/detections/rpc-reconnaissance.md) | Sysmon EID 3 | Custom Wazuh | [100100](wazuh/rpc-recon-100100.xml) | T1046 | Completed |
| [PowerShell ExecutionPolicy Bypass](../docs/detections/powershell-executionpolicy-bypass.md) | Sysmon EID 1 | Custom Wazuh (+ built-in `92027`) | [100101](wazuh/powershell-executionpolicy-bypass-100101.xml) | T1059.001 | Completed |
| [Password guessing](../docs/detections/password-guessing.md) | Windows Security 4625 | Built-in Wazuh correlation | `60204` (via `60122`) | T1110 | Completed |

## Custom Wazuh Rules

| Rule | File | Notes |
|------|------|-------|
| 100100 | [wazuh/rpc-recon-100100.xml](wazuh/rpc-recon-100100.xml) | Lab-specific; hardcoded ATTACK01 source IP |
| 100101 | [wazuh/powershell-executionpolicy-bypass-100101.xml](wazuh/powershell-executionpolicy-bypass-100101.xml) | Depends on built-in `92027` |

## Sigma

| Rule | File | Notes |
|------|------|-------|
| PowerShell ExecutionPolicy Bypass | [sigma/powershell-executionpolicy-bypass.yml](sigma/powershell-executionpolicy-bypass.yml) | Portable equivalent; not claimed as production-deployed |

## Notes

- Distinguish **custom** rules (`100100`, `100101`) from **built-in** Wazuh detections (`92027`, `60122`, `60204`)
- RPC rule `100100` detects inbound TCP/135 from ATTACK01; Wireshark confirms EPM Lookup semantics
- Password-guessing detection uses built-in correlation only

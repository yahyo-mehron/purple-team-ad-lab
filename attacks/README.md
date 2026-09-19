# Attack Simulations

Adversary simulation notes for defensive validation inside the isolated lab environment.

## Completed Simulations

| Attack | Source | Target | Detection report |
|--------|--------|--------|------------------|
| RPC Endpoint Mapper reconnaissance | ATTACK01 | WS01 | [rpc-reconnaissance.md](../docs/detections/rpc-reconnaissance.md) |
| Suspicious PowerShell (`ExecutionPolicy Bypass`) | WS01 | WS01 | [powershell-executionpolicy-bypass.md](../docs/detections/powershell-executionpolicy-bypass.md) |
| Password guessing (SMB/NTLM) | ATTACK01 | WS01 (`PURPLE\alice`) | [password-guessing.md](../docs/detections/password-guessing.md) |

Detailed attack commands, telemetry and detection analysis live in the detection reports above.

All activity is performed only inside the lab for detection engineering and purple-team validation.

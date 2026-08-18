# Timeline

| Order | Source | Activity | Result |
|---:|---|---|---|
| 1 | File System | `C:\AttackerCleanupLab` created | Controlled workspace established |
| 2 | File System | `test-payload.txt` created | Controlled artifact created |
| 3 | File System | `suspicious-script.ps1` created | Controlled script created |
| 4 | File System | Baseline metadata collected | Pre-cleanup state documented |
| 5 | File System | Baseline SHA-256 hashes collected | Artifact identifiers recorded |
| 6 | PowerShell | Normal script execution attempted | Execution blocked by policy |
| 7 | PowerShell | Process-level `-ExecutionPolicy Bypass` used | Controlled script executed |
| 8 | Event Viewer | Sysmon Event ID 1 observed | PowerShell process activity recorded |
| 9 | Event Viewer | PowerShell Event ID 4104 observed | Script-level activity recorded |
| 10 | PowerShell | Sysmon Event ID 3 checked | Network telemetry investigated |
| 11 | File System | `temporary-artifact.txt` created | Additional controlled artifact |
| 12 | File System | Cleanup initiated | Lab artifacts removed |
| 13 | File System | Cleanup verified | Lab directory no longer contained test files |
| 14 | PowerShell | Event ID 4104 checked again | No corresponding post-cleanup event observed |
| 15 | Event Viewer | Sysmon Event ID 1 reviewed | Earlier process evidence remained available |
| 16 | Event Viewer | Security Event ID 4688 reviewed | Relevant event not obtained |
| 17 | PowerShell | Sysmon Event ID 3 reviewed | Network activity checked |
| 18 | DFIR Analysis | Pre- and post-cleanup evidence compared | Cleanup behavior reconstructed |
| 19 | DFIR Analysis | Evidence gaps documented | Missing telemetry recorded |
| 20 | DFIR Analysis | Investigation concluded | Evidence-supported assessment completed |


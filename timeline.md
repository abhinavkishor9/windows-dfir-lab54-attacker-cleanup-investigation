# Timeline — Lab 54 Attacker Cleanup Investigation

## Timeline Purpose

This timeline records the sequence of controlled artifact creation, PowerShell execution, telemetry collection, cleanup, and post-cleanup investigation performed during Lab 54.

Actual timestamps from Event Viewer should be inserted where available.

## Investigation Timeline

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

## Phase 1 — Artifact Creation

### T+00 — Investigation Directory Created

The controlled investigation directory was created:

`C:\AttackerCleanupLab`

This directory was the only location targeted by the cleanup simulation.

### T+05 — Test Artifacts Created

Harmless files were created:

- `test-payload.txt`
- `suspicious-script.ps1`

The files were created solely for investigation purposes.

### T+10 — Baseline Metadata Collected

The following information was recorded:

- Filename
- File size
- Creation time
- Last write time

### T+15 — Baseline Hashes Collected

SHA-256 hashes were calculated for the controlled files.

This preserved evidence of the files' original state.

---

# Phase 2 — Script Execution

### T+20 — Initial Execution Attempt

The PowerShell script was executed using the normal PowerShell command.

Execution was blocked because the system's Execution Policy prevented script execution.

### T+25 — Execution Policy Bypass Used

The controlled script was executed using:

    powershell.exe -ExecutionPolicy Bypass -File "C:\AttackerCleanupLab\suspicious-script.ps1"

This generated the process and PowerShell telemetry used later in the investigation.

### T+30 — Sysmon Event ID 1 Observed

Sysmon Event ID 1 was observed through Event Viewer.

The event was investigated for:

- Process
- Parent process
- Command line
- PID
- User
- Timestamp

### T+35 — PowerShell Event ID 4104 Observed

PowerShell Event ID 4104 was observed during the execution phase.

The event provided script-level context for the controlled activity.

---

# Phase 3 — Additional Artifact Creation

### T+40 — Temporary Artifact Created

A second harmless artifact was created:

`temporary-artifact.txt`

This provided another file that could be removed during the cleanup simulation.

### T+45 — Post-Creation Baseline Collected

The investigation directory was checked again.

Metadata and hashes were recorded to establish the complete pre-cleanup state.

---

# Phase 4 — Cleanup Simulation

### T+50 — Cleanup Started

The controlled artifacts were deleted from:

`C:\AttackerCleanupLab`

Only the lab files were targeted.

No system logs, security infrastructure, VSS data, or Windows system files were modified.

### T+55 — Cleanup Verified

The directory was checked again.

The test artifacts were no longer present.

The filesystem therefore contained less direct evidence than it had before cleanup.

---

# Phase 5 — Post-Cleanup Investigation

### T+60 — PowerShell Event ID 4104 Rechecked

PowerShell Event ID 4104 was searched again.

The expected post-cleanup event was not observed.

This was documented as a telemetry limitation.

### T+65 — Sysmon Event ID 1 Reviewed

Sysmon Event ID 1 was reviewed again.

Earlier process-creation evidence remained useful for reconstructing the execution stage.

### T+70 — Windows Security Event ID 4688 Investigated

Security Event ID 4688 was searched but the relevant event was not obtained.

This created an additional process-creation telemetry gap.

### T+75 — Sysmon Event ID 3 Checked

Sysmon Event ID 3 was queried through PowerShell to identify network activity around the execution and cleanup period.

---

# Phase 6 — Evidence Correlation

### T+80 — Filesystem State Compared

The investigation compared:

`Before Cleanup`

against:

`After Cleanup`

The comparison demonstrated that the controlled files had been removed.

### T+85 — Baseline Evidence Reviewed

Previously collected:

- File metadata
- SHA-256 hashes
- File names
- File paths
- Creation timestamps

were used as preserved evidence of the pre-cleanup state.

### T+90 — Process Evidence Correlated

Sysmon Event ID 1 and PowerShell Event ID 4104 were correlated with the execution timeframe.

### T+95 — Network Evidence Reviewed

Sysmon Event ID 3 was reviewed for supporting network activity.

### T+100 — Final Assessment

The investigation determined that:

- Files existed before cleanup.
- The PowerShell script executed after a process-level policy bypass.
- Sysmon Event ID 1 recorded process activity.
- PowerShell Event ID 4104 recorded script activity before cleanup.
- Sysmon Event ID 3 was checked for network activity.
- The controlled artifacts were subsequently deleted.
- Security Event ID 4688 was not obtained.
- Post-cleanup 4104 telemetry was not observed.

---

# Final Timeline Summary

| Phase | Evidence |
|---|---|
| Artifact Creation | Controlled files and script created |
| Baseline | Metadata and hashes preserved |
| Execution | PowerShell execution performed with process-level bypass |
| Process Evidence | Sysmon Event ID 1 observed |
| Script Evidence | PowerShell Event ID 4104 observed |
| Network Evidence | Sysmon Event ID 3 checked |
| Cleanup | Controlled files deleted |
| Post-Cleanup | Files no longer present |
| Telemetry Review | Earlier process/script evidence investigated |
| Evidence Gaps | 4688 unavailable; post-cleanup 4104 not observed |
| Final Assessment | Cleanup behavior reconstructed from surviving evidence |

---

# Investigation Conclusion

The timeline demonstrates that a cleanup action can remove files from the filesystem while leaving other traces of the preceding activity.

The most important evidence in this lab came from the information preserved before deletion and the process/script telemetry generated during execution.

The investigation therefore reinforces a key DFIR principle:

> **Evidence preservation before cleanup is critical because filesystem artifacts may disappear while historical telemetry remains available.**

The absence of a later event should not be interpreted as proof that the associated activity did not occur. Instead, the investigator should document the telemetry gap and rely on the surviving evidence to reconstruct the activity.

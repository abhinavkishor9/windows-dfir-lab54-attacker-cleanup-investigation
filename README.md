# Windows DFIR Lab 54 — Attacker Cleanup Investigation

## Overview

Attacker cleanup refers to actions performed after malicious activity to remove files, scripts, tools, or other traces that could help an investigator understand what happened. In a real intrusion, cleanup may include deleting payloads, removing temporary files, clearing command history, deleting persistence mechanisms, or attempting to interfere with logging.

This lab safely simulates a small portion of that behavior by creating harmless investigation artifacts inside `C:\AttackerCleanupLab`, executing a controlled PowerShell script, collecting baseline evidence, and then deleting only the lab artifacts.

The investigation focused on determining what evidence remained after cleanup and whether process and PowerShell telemetry could reconstruct activity even after the files themselves were removed.

---

# Executive Summary

A controlled cleanup scenario was created using harmless files and a PowerShell script inside a dedicated investigation directory. Baseline file metadata and SHA-256 hashes were collected before execution and cleanup.

The PowerShell script initially failed to execute because the system's PowerShell Execution Policy blocked script execution. The script was then executed using a process-level `-ExecutionPolicy Bypass`, without permanently changing the system-wide policy.

Sysmon Event ID 1 and PowerShell Event ID 4104 were observed during the investigation. Sysmon Event ID 3 was also checked through PowerShell. Windows Security Event ID 4688 was not successfully obtained.

After the cleanup simulation, the lab artifacts were removed from the filesystem. The investigation then examined which telemetry remained and whether the deleted files could still be associated with the earlier execution.

---

# Investigation Objectives

- Establish the state of controlled artifacts before cleanup.
- Record file metadata and hashes before deletion.
- Identify the process used to execute the controlled script.
- Examine PowerShell Script Block Logging.
- Investigate Sysmon process-creation telemetry.
- Review available network telemetry.
- Determine what evidence remains after files are deleted.
- Compare pre-cleanup and post-cleanup filesystem states.
- Document the effect of PowerShell Execution Policy on the investigation.
- Identify missing telemetry and evidence limitations.
- Determine whether the cleanup activity can be reconstructed from surviving evidence.

---

# Skills Demonstrated

- Windows DFIR
- Attacker Cleanup Investigation
- PowerShell Investigation
- PowerShell Script Block Logging
- Sysmon Analysis
- Sysmon Event ID 1 Analysis
- Sysmon Event ID 3 Analysis
- Filesystem Timeline Analysis
- SHA-256 Hashing
- Process Correlation
- Event Viewer Investigation
- Evidence Preservation
- Artifact Recovery Analysis
- Telemetry Gap Documentation
- DFIR Timeline Construction

---

# Tools Used

- Windows
- PowerShell
- Event Viewer
- Sysmon
- VSSAdmin
- File System
- SHA-256 hashing

---

# Lab Environment

| Component | Details |
|---|---|
| Operating System | Windows |
| Investigation Type | Host-Based DFIR |
| Primary Activity | Controlled Attacker Cleanup Simulation |
| Investigation Directory | `C:\AttackerCleanupLab` |
| Primary Telemetry | Sysmon Event ID 1 |
| PowerShell Telemetry | Event ID 4104 |
| Supporting Telemetry | Sysmon Event ID 3 |
| Security Event ID 4688 | Not obtained |

---

# Investigation Scenario

A Windows workstation is suspected of having temporary artifacts created and later removed after suspicious activity. The SOC analyst needs to determine whether files existed before the cleanup, which process created or executed them, when they disappeared, and whether endpoint telemetry retained evidence after the filesystem was cleaned.

A controlled simulation was performed using harmless files and a PowerShell script. All cleanup actions were limited to the dedicated lab directory.

No Windows event logs, real system files, security tools, or recovery infrastructure were intentionally deleted or modified.

---

# Investigation Workflow

1. Create the investigation directory.
2. Create harmless test artifacts.
3. Create a controlled PowerShell script.
4. Collect baseline file metadata.
5. Collect baseline SHA-256 hashes.
6. Execute the controlled script.
7. Investigate Sysmon Event ID 1.
8. Investigate PowerShell Event ID 4104.
9. Check Windows Security Event ID 4688.
10. Check Sysmon Event ID 3.
11. Create additional temporary artifacts.
12. Perform controlled cleanup.
13. Confirm filesystem artifacts were removed.
14. Re-check available telemetry.
15. Compare pre-cleanup and post-cleanup evidence.
16. Reconstruct the activity timeline.
17. Document telemetry gaps and limitations.
18. Produce an evidence-supported assessment.

---

# Controlled Artifacts

The investigation used a dedicated directory:

`C:\AttackerCleanupLab`

The following types of harmless artifacts were created:

- `test-payload.txt`
- `suspicious-script.ps1`
- `temporary-artifact.txt`

The files contained only controlled lab data.

---

# Baseline Evidence

Before cleanup, file metadata was collected using PowerShell.

Example:

    Get-ChildItem "C:\AttackerCleanupLab" -File |
    Select-Object Name, Length, CreationTime, LastWriteTime

SHA-256 hashes were also collected:

    Get-ChildItem "C:\AttackerCleanupLab" -File |
    Get-FileHash -Algorithm SHA256

This created a record of the files before cleanup occurred.

---

# PowerShell Execution Policy Issue

The initial attempt to execute the PowerShell script normally was blocked by the system's Execution Policy.

The original execution attempt resulted in a SecurityError indicating that running scripts was disabled on the system.

The execution policy was not permanently changed.

The controlled script was subsequently executed using:

    powershell.exe -ExecutionPolicy Bypass -File "C:\AttackerCleanupLab\suspicious-script.ps1"

The use of a process-level bypass was documented because command-line execution options can themselves become useful forensic evidence.

---

# Sysmon Event ID 1

Sysmon Event ID 1 was observed through Event Viewer.

The event was used to investigate the PowerShell process created during the controlled execution.

Relevant fields included:

- Process Image
- Command Line
- Parent Image
- Process ID
- User
- Timestamp

This provided process-level evidence even though Windows Security Event ID 4688 was not successfully obtained.

---

# PowerShell Event ID 4104

PowerShell Event ID 4104 was observed during the investigation.

The event was reviewed for evidence related to the controlled script execution.

Relevant information can include:

- Script content
- Script path
- Timestamp
- PowerShell execution context
- Commands executed

The investigation also checked for PowerShell Event ID 4104 after the cleanup activity.

The absence of a corresponding 4104 event after cleanup was documented as a telemetry observation rather than evidence that the cleanup had not occurred.

---

# Windows Security Event ID 4688

Windows Security Event ID 4688 was investigated but was not successfully obtained for the PowerShell activity.

This created an evidence gap.

The investigation therefore relied on:

- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- Filesystem evidence
- Baseline metadata
- SHA-256 hashes

The absence of 4688 was not interpreted as proof that process creation did not occur.

---

# Sysmon Event ID 3

Sysmon Event ID 3 was checked through PowerShell.

The purpose was to determine whether network activity occurred around the controlled execution and cleanup timeframe.

Relevant fields include:

- Process
- Process ID
- Source IP
- Destination IP
- Destination Port
- Protocol
- Timestamp

Network activity was treated as supporting evidence only.

A network connection does not independently prove that data was exfiltrated.

---

# Cleanup Simulation

The cleanup stage removed only the controlled lab artifacts.

The files were removed from:

`C:\AttackerCleanupLab`

The cleanup was intentionally restricted to the lab directory.

No real Windows logs, system files, security tools, VSS data, or backup infrastructure were targeted.

After cleanup, the directory was checked again to confirm that the files were no longer present.

---

# Post-Cleanup Investigation

After the files were deleted, the filesystem no longer contained the controlled artifacts.

The investigation then checked whether telemetry still contained references to the earlier activity.

The following evidence sources were reviewed:

- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- Windows Security Event ID 4688
- Previously collected file metadata
- Previously collected SHA-256 hashes

This demonstrated an important DFIR principle:

> Deleting a file does not necessarily remove all evidence that the file or associated process existed.

---

# Evidence Correlation

The investigation followed this evidence model:

    Artifacts Created
           |
           v
    Baseline Metadata
           |
           v
    SHA-256 Hashes
           |
           v
    PowerShell Execution
           |
           +---- Sysmon Event ID 1
           |
           +---- PowerShell Event ID 4104
           |
           +---- Sysmon Event ID 3
           |
           v
    Cleanup Activity
           |
           v
    Files Deleted
           |
           v
    Post-Cleanup Telemetry
           |
           v
    Timeline Reconstruction

The objective was to determine which evidence survived the cleanup.

---

# Observed Telemetry

| Source | Event ID | Status | Purpose |
|---|---:|---|---|
| Sysmon | 1 | Observed | Process creation |
| PowerShell | 4104 | Observed before cleanup | Script execution |
| PowerShell | 4104 | Not observed after cleanup | Telemetry gap |
| Sysmon | 3 | Checked through PowerShell | Network activity |
| Windows Security | 4688 | Not obtained | Process creation evidence |

---

# Key Findings

- Controlled cleanup artifacts were successfully created.
- Baseline metadata and hashes were collected before cleanup.
- Normal execution of the PowerShell script was blocked by Execution Policy.
- The script was executed using a process-level `-ExecutionPolicy Bypass`.
- Sysmon Event ID 1 was observed.
- PowerShell Event ID 4104 was observed before cleanup.
- Sysmon Event ID 3 was checked through PowerShell.
- Windows Security Event ID 4688 was not successfully obtained.
- The controlled artifacts were removed from the filesystem during the cleanup simulation.
- PowerShell Event ID 4104 was not observed after the cleanup stage.
- The missing post-cleanup event was documented as a telemetry limitation.
- Earlier process and script telemetry demonstrated how cleanup activity may remain reconstructable after files disappear.

---

# DFIR Interpretation

The key investigative distinction is between:

`File no longer exists`

and:

`No evidence of the file ever existing`

These are not equivalent.

In a real incident, an attacker may delete a malicious script after execution. Even if the file is gone, investigators may still find references to it in process creation telemetry, PowerShell logs, security events, filesystem metadata, or other endpoint artifacts.

The lab therefore demonstrates why evidence should be collected and preserved before cleanup whenever possible.

---

# MITRE ATT&CK Relevance

The investigation is broadly relevant to:

**T1070 — Indicator Removal**

and:

**T1070.004 — File Deletion**

PowerShell execution may also be relevant to:

**T1059.001 — PowerShell**

These techniques should only be mapped when the observed behavior supports them. Deleting a file inside a controlled lab does not establish malicious activity.

---

# Evidence Limitations

The investigation had several telemetry limitations.

Windows Security Event ID 4688 was not successfully obtained for the PowerShell activity.

PowerShell Event ID 4104 was observed during the execution phase but was not observed after cleanup.

Sysmon Event ID 3 was checked through PowerShell but was treated only as supporting network evidence.

The controlled cleanup occurred inside a lab directory, so the activity does not represent a real compromise.

---

# Conclusion

This investigation demonstrated how attacker cleanup can reduce visible filesystem evidence while leaving other forms of telemetry behind.

The controlled PowerShell activity generated process and script telemetry before the lab artifacts were deleted. After cleanup, the files disappeared from the filesystem, but previously collected evidence remained useful for reconstructing the sequence of events.

The investigation also demonstrated that missing telemetry must be documented rather than treated as proof that an activity did not occur.

The central DFIR lesson is:

> A deleted artifact is not necessarily an erased artifact.

A strong investigation preserves baseline evidence, correlates process and script telemetry, examines surviving logs, and reconstructs what happened before and after cleanup.

---

# Skills Demonstrated

- Attacker Cleanup Investigation
- Windows DFIR
- PowerShell Investigation
- PowerShell Event ID 4104 Analysis
- Sysmon Event ID 1 Analysis
- Sysmon Event ID 3 Analysis
- File Deletion Investigation
- SHA-256 Hashing
- Filesystem Timeline Analysis
- Evidence Preservation
- Event Correlation
- Telemetry Gap Documentation
- Indicator Removal Analysis
- DFIR Timeline Construction

---

# Repository Structure

    windows-dfir-lab54-attacker-cleanup-investigation/
    |
    +-- README.md
    +-- investigation-notes.md
    +-- troubleshooting-notes.md
    +-- timeline.md

---

# Disclaimer

This lab was performed in a controlled environment using harmless test artifacts. Cleanup actions were restricted to `C:\AttackerCleanupLab`. No real Windows logs, system files, recovery mechanisms, or security controls were intentionally modified or deleted.

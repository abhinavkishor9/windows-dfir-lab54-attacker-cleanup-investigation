# windows-dfir-lab54-attacker-cleanup-investigation
## Overview

After performing an intrusion, an attacker may try to remove traces of their activity. This is commonly called attacker cleanup or indicator removal.

Examples in a real incident can include:

Deleting malicious files.
Removing temporary payloads.
Deleting scripts.
Removing scheduled tasks.
Clearing or tampering with logs.
Removing downloaded tools.
Modifying timestamps or filenames.
Removing persistence mechanisms.
Cleaning command history.

From a DFIR perspective, cleanup is important because the absence of an artifact can itself become an investigative clue.

For example:

Suspicious script executed
        ↓
Payload created
        ↓
Payload executed
        ↓
Payload deleted

If the analyst only looks at the filesystem after the deletion, the payload may appear never to have existed.

Therefore, we want to correlate:

Process execution → File creation → File modification → Cleanup action → Remaining evidence

This lab safely simulates a small portion of that behavior by creating harmless investigation artifacts inside `C:\AttackerCleanupLab`, executing a controlled PowerShell script, collecting baseline evidence, and then deleting only the lab artifacts.

The investigation focused on determining what evidence remained after cleanup and whether process and PowerShell telemetry could reconstruct activity even after the files themselves were removed.

---


# Investigation Objectives

- Establish what artifacts existed on the system before the cleanup activity.
- Determine how the controlled script was executed and under which PowerShell security conditions.
- Identify the process responsible for the execution and reconstruct its execution context.
- Examine whether PowerShell logging preserved evidence of the activity.
- Determine what traces remained after the test artifacts were deleted.
- Compare the filesystem state before and after cleanup.
- Use previously collected hashes and metadata as preserved evidence of deleted artifacts.
- Investigate whether any network activity occurred during the execution or cleanup period.
- Assess the usefulness of historical telemetry when the original files are no longer present.
- Identify which expected evidence sources were unavailable and assess their impact on the investigation.
- Distinguish artifact deletion from complete removal of forensic evidence.
- Determine whether the available evidence is sufficient to reconstruct the cleanup sequence.

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

A Windows workstation is suspected of having temporary files and scripts removed after suspicious activity. The SOC analyst needs to determine whether the deleted artifacts were part of a legitimate administrative task or an attempt to hide previous activity.

The investigation focuses on:

What files existed before the cleanup.
Which process executed the associated script.
What PowerShell activity was recorded.
When the cleanup occurred.
Which artifacts disappeared from the filesystem.
What telemetry remained after the files were deleted.
Whether the surviving evidence is sufficient to reconstruct the sequence of activity.

The analyst must determine whether the evidence supports intentional cleanup behavior and clearly distinguish confirmed observations from evidence that is no longer available.

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

# MITRE ATT&CK Relevance

The investigation is broadly relevant to:

**T1070 — Indicator Removal**

and:

**T1070.004 — File Deletion**

PowerShell execution may also be relevant to:

**T1059.001 — PowerShell**

These techniques should only be mapped when the observed behavior supports them. Deleting a file inside a controlled lab does not establish malicious activity.

---

# Disclaimer

This lab was performed in a controlled environment using harmless test artifacts. Cleanup actions were restricted to `C:\AttackerCleanupLab`. No real Windows logs, system files, recovery mechanisms, or security controls were intentionally modified or deleted.

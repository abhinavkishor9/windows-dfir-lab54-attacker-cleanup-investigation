# Troubleshooting Notes 

## 1. PowerShell Script Was Initially Blocked

### Problem

The first attempt to execute:

    powershell.exe -File "C:\AttackerCleanupLab\suspicious-script.ps1"

failed because PowerShell script execution was disabled by the configured Execution Policy.

### Observed Error

The system returned a SecurityError stating that the script could not be loaded because running scripts was disabled.

### Resolution

The script was executed using a process-level bypass:

    powershell.exe -ExecutionPolicy Bypass -File "C:\AttackerCleanupLab\suspicious-script.ps1"

### Why This Approach Was Used

The bypass applied only to the PowerShell process used for the controlled lab.

The system-wide Execution Policy was not permanently changed.

### DFIR Lesson

Security controls can directly affect the execution of investigation artifacts and the telemetry generated during testing.

---

## 2. Windows Security Event ID 4688 Was Not Obtained

### Problem

Event ID 4688 was investigated but was not successfully obtained for the PowerShell activity.

### Possible Causes

- Process Creation auditing may not be configured.
- The Security log may not contain the relevant event.
- The event may have been overwritten.
- The event may have been filtered.
- The relevant process activity may not have been captured.

### Resolution

The investigation continued using:

- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- Filesystem evidence
- Baseline hashes

### DFIR Lesson

Missing 4688 should be recorded as a telemetry gap.

It should not be treated as evidence that PowerShell did not execute.

---

## 3. PowerShell Event ID 4104 Was Present Before Cleanup

### Observation

PowerShell Event ID 4104 was observed during the controlled script execution.

### Significance

The event provided script-level evidence that could help establish:

- Script execution
- Script path
- Timestamp
- PowerShell activity

### DFIR Lesson

Script Block Logging can preserve evidence of PowerShell activity even when the script file itself is later deleted.

---

## 4. PowerShell Event ID 4104 Was Not Observed After Cleanup

### Observation

After the cleanup simulation, another search for PowerShell Event ID 4104 did not return the expected post-cleanup event.

### Interpretation

This does not prove that cleanup did not occur.

Possible explanations include:

- Logging behavior
- Event timing
- Event filtering
- Script execution details not being logged
- Event retention limitations

### Resolution

The missing telemetry was documented as an evidence limitation.

The investigation relied on earlier telemetry captured during execution.

---

## 5. Sysmon Event ID 1 Was Observed

### Observation

Sysmon Event ID 1 was available through Event Viewer.

### Purpose

It was used to investigate the PowerShell process associated with the controlled execution.

Relevant fields included:

- Image
- Command Line
- Parent Image
- Process ID
- User
- Timestamp

### DFIR Lesson

Process creation telemetry can remain useful even when the executable or script is deleted afterward.

---

## 6. Sysmon Event ID 3 Was Checked Through PowerShell

### Observation

Sysmon Event ID 3 was queried using PowerShell.

### Purpose

The event was reviewed for network activity around the execution and cleanup timeframe.

### Interpretation

Any observed network connection would require additional analysis.

A network connection alone does not establish data exfiltration or malicious activity.

---

## 7. Cleanup Removed the Test Files

### Observation

After the cleanup simulation, the controlled artifacts were no longer present in:

`C:\AttackerCleanupLab`

### Significance

This demonstrated how filesystem evidence can disappear after deletion.

### DFIR Lesson

The post-cleanup filesystem may provide less evidence than the system provided during the original activity.

This is why early evidence preservation matters.

---

## 8. Baseline Evidence Was Important

### Observation

File metadata and SHA-256 hashes were collected before cleanup.

### Significance

Even after the files were deleted, the investigation still had:

- Original filenames
- Original paths
- File sizes
- Timestamps
- Hashes

### DFIR Lesson

Baseline collection provides evidence that may no longer be available after cleanup.

---

## 9. Execution Policy Bypass Created Useful Evidence

### Observation

The controlled script required:

    -ExecutionPolicy Bypass

### Significance

If command-line telemetry is available, this parameter may become an important investigative indicator.

### Important Context

`-ExecutionPolicy Bypass` is not automatically malicious.

Administrators and analysts may use it for legitimate reasons.

It becomes more suspicious when combined with:

- Unknown script
- Suspicious parent process
- User-writable location
- Encoded commands
- Persistence
- Network activity
- Other malicious behavior

---

## 10. Do Not Clear Windows Logs During the Lab

### Safety Rule

The cleanup simulation was intentionally restricted to:

`C:\AttackerCleanupLab`

Do not use commands such as:

    wevtutil cl Security

or:

    wevtutil cl System

or other commands designed to clear Windows event logs.

### Reason

The objective is to investigate attacker cleanup safely, not to destroy real forensic evidence.

---

## 11. Do Not Delete Sysmon Logs

Sysmon logs were kept intact.

This allows the investigation to demonstrate the difference between:

    File deletion

and:

    Log evidence preservation

---

## 12. Do Not Disable Security Tools

The cleanup simulation did not involve:

- Disabling Defender
- Stopping Sysmon
- Stopping Windows Event Log
- Modifying security policies
- Deleting system files

This ensures that the lab remains safe and reproducible.

---


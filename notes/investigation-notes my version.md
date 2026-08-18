# Investigation Notes 

## Investigation Directory

The controlled workspace was:

`C:\AttackerCleanupLab`

All files used during the simulation were restricted to this directory.

## Initial Artifacts

The investigation created controlled artifacts including:

- `test-payload.txt`
- `suspicious-script.ps1`
- `temporary-artifact.txt`

These files contained only harmless lab data.

## Baseline Collection

Before cleanup, the directory contents were recorded using:

    Get-ChildItem "C:\AttackerCleanupLab" -File |
    Select-Object Name, Length, CreationTime, LastWriteTime

SHA-256 hashes were also collected:

    Get-ChildItem "C:\AttackerCleanupLab" -File |
    Get-FileHash -Algorithm SHA256

This established a record of the artifacts before deletion.

## Script Execution

The PowerShell script was initially executed normally.

The system prevented execution because the configured PowerShell Execution Policy disabled script execution.

The resulting SecurityError was documented as part of the investigation.

## Execution Policy Bypass

The controlled script was executed using:

    powershell.exe -ExecutionPolicy Bypass -File "C:\AttackerCleanupLab\suspicious-script.ps1"

The bypass applied to the individual PowerShell process.

No permanent system-wide Execution Policy change was made.

This execution method was important because the command line itself can become process-level forensic evidence.

## Sysmon Event ID 1

Sysmon Event ID 1 was observed through Event Viewer.

The event was used to investigate the PowerShell process created during the controlled execution.

Relevant fields included:

- Process Image
- Command Line
- Parent Image
- Process ID
- User
- Timestamp

This provided process evidence even though Security Event ID 4688 was unavailable.

## PowerShell Event ID 4104

PowerShell Event ID 4104 was observed during the execution stage.

The event was reviewed for script activity related to:

- `AttackerCleanupLab`
- `suspicious-script.ps1`
- Script execution
- Controlled lab activity

The event provided script-level context that complemented Sysmon Event ID 1.

## Post-Cleanup 4104 Observation

After the cleanup stage, PowerShell Event ID 4104 was checked again.

A corresponding post-cleanup 4104 event was not observed.

This was recorded as a telemetry gap.

The absence of the event was not interpreted as proof that the cleanup did not occur.

## Windows Security Event ID 4688

Event ID 4688 was investigated but was not successfully obtained for the PowerShell activity.

This created an evidence limitation.

The process investigation therefore relied primarily on Sysmon Event ID 1 and PowerShell Event ID 4104.

## Sysmon Event ID 3

Sysmon Event ID 3 was checked through PowerShell:

    Get-WinEvent -FilterHashtable @{
        LogName = "Microsoft-Windows-Sysmon/Operational"
        Id = 3
    } -MaxEvents 100 |
    Select-Object TimeCreated, Id, Message

The event was reviewed for network activity around the controlled execution and cleanup timeframe.

Network activity was treated as supporting evidence.

## Cleanup Stage

After baseline collection and execution, the controlled files were deleted.

The cleanup was restricted to:

`C:\AttackerCleanupLab`

The following artifacts were removed:

- `test-payload.txt`
- `temporary-artifact.txt`
- `suspicious-script.ps1`

No Windows event logs, system files, security controls, VSS data, or real backup infrastructure were modified.

## Post-Cleanup State

The investigation directory was checked after deletion.

The previously existing files were no longer present.

This demonstrated the difference between the filesystem state before and after cleanup.

## Surviving Evidence

After cleanup, the investigation reviewed:

- Sysmon Event ID 1
- PowerShell Event ID 4104
- Sysmon Event ID 3
- Security Event ID 4688
- Previously collected metadata
- Previously collected SHA-256 hashes

The purpose was to determine whether the activity could still be reconstructed despite the deleted files.


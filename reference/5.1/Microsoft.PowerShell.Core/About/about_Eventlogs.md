---
description: Windows PowerShell creates a Windows event log that is named Windows PowerShell to record Windows PowerShell events. You can view this log in Event Viewer or by using cmdlets that get events, such as the `Get-EventLog` cmdlet. By default, Windows PowerShell engine and provider events are recorded in the event log, but you can use the event log preference variables to customize the event log. For example, you can add events about Windows PowerShell commands.
Locale: en-US
ms.date: 12/08/2023
online version: https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_eventlogs?view=powershell-5.1&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about_Eventlogs
---

# about_Eventlogs

## Short description
This article describes how PowerShell logs events to the Windows Event log.

## Long description

Windows PowerShell creates a Windows event log that's named "Windows
PowerShell" to record Windows PowerShell events. You can view this log in Event
Viewer or by using cmdlets that get events, such as the `Get-EventLog` cmdlet.
By default, Windows PowerShell engine and provider events are recorded in the
event log, but you can use the event log preference variables to customize the
event log. For example, you can add events about Windows PowerShell commands.

The Windows PowerShell event log records details of Windows PowerShell
operations, such as starting and stopping the program engine and starting and
stopping the Windows PowerShell providers. You can also log details about
Windows PowerShell commands.


## Viewing the Windows PowerShell Event Log

You can view the Windows PowerShell event log in Event Viewer or by using the
`Get-EventLog` and `Get-WmiObject` cmdlets. To view the contents of the Windows
PowerShell log, type:

```powershell
Get-EventLog -LogName "Windows PowerShell"
```

To examine the events and their properties, use the `Sort-Object` cmdlet, the
`Group-Object` cmdlet, and the cmdlets that contain the `Format` verb (the
`Format` cmdlets).

For example, to view the events in the log grouped by the event ID, type:

```powershell
Get-EventLog "Windows PowerShell" | Format-Table -GroupBy EventID
```

Or, type:

```powershell
Get-EventLog "Windows PowerShell" |
  Sort-Object EventID |
  Group-Object EventID
```

To view all the classic event logs, type:

```powershell
Get-EventLog -List
```

You can also use the `Get-WmiObject` cmdlet to use the event-related Windows
Management Instrumentation (WMI) classes to examine the event log. For example,
to view all the properties of the event log file, type:

```powershell
Get-WmiObject Win32_NTEventlogFile |
  where LogFileName -EQ "Windows PowerShell" |
  Format-List -Property *
```

To find the Win32 event-related WMI classes, type:

```powershell
Get-WmiObject -List | where Name -Like "Win32*Event*"
```

For more information, see [Get-EventLog][05] and [Get-WmiObject][06].

## Selecting Events for the Windows PowerShell Event Log

You can use the event log preference variables to determine which events are
recorded in the Windows PowerShell event log.

There are six event log preference variables; two variables for each of the
three logging components: the engine (the Windows PowerShell program), the
providers, and the commands. The `LifecycleEvent` variables log normal starting
and stopping events. The `Health` variables log error events.

The following table lists the event log preference variables.

|           Variable           |                   Description                   |
| ---------------------------- | ----------------------------------------------- |
| `$LogEngineLifecycleEvent`   | Logs the start and stop of PowerShell           |
| `$LogEngineHealthEvent`      | Logs PowerShell program errors                  |
| `$LogProviderLifecycleEvent` | Logs the start and stop of PowerShell providers |
| `$LogProviderHealthEvent`    | Logs PowerShell provider errors                 |
| `$LogCommandLifecycleEvent`  | Logs the starting and completion of commands    |
| `$LogCommandHealthEvent`     | Logs command errors                             |

(For information about Windows PowerShell providers, see
[about_Providers][01].)

By default, only the following event types are enabled:

- `$LogEngineLifecycleEvent`
- `$LogEngineHealthEvent`
- `$LogProviderLifecycleEvent`
- `$LogProviderHealthEvent`

To enable an event type, set the preference variable for that event type to
`$true`. For example, to enable command life-cycle events, type:

```powershell
$LogCommandLifecycleEvent
```

Or, type:

```powershell
$LogCommandLifecycleEvent = $true
```

To disable an event type, set the preference variable for that event type to
`$false`. For example, to disable command life-cycle events, type:

```powershell
$LogProviderLifecycleEvent = $false
```

You can disable any event, except for the events that indicate that the Windows
PowerShell engine and the core providers started. These events generate before
the Windows PowerShell profiles run and before the host program is ready to
accept commands.

The variable settings apply only for the current Windows PowerShell session. To
apply them to all Windows PowerShell sessions, add them to your Windows
PowerShell profile.

## Logging Module Events

Beginning in Windows PowerShell 3.0, you can record execution events for the
cmdlets and functions in Windows PowerShell modules and snap-ins by setting the
**LogPipelineExecutionDetails** property of modules and snap-ins to `$true`. In
Windows PowerShell 2.0, this feature is available only for snap-ins.

When the **LogPipelineExecutionDetails** property value is `$true`, Windows
PowerShell writes cmdlet and function execution events in the session to the
Windows PowerShell log in Event Viewer. The setting is effective only in the
current session.

To enable logging of execution events of cmdlets and functions in a module, use
the following command sequence.

```powershell
Import-Module Microsoft.PowerShell.Archive
$m = Get-Module Microsoft.PowerShell.Archive
$m.LogPipelineExecutionDetails = $true
```

To enable logging of execution events of cmdlets in a snap-in, use the
following command sequence.

```powershell
$m = Get-PSSnapin Microsoft.PowerShell.Core
$m.LogPipelineExecutionDetails = $true
```

To disable logging, use the same command sequence to set the property value to
`$false`.

You can also use the **Turn on Module Logging** Group Policy setting to enable
and disable module and snap-in logging. The policy value includes a list of
module and snap-in names. Wildcards are supported.

When **Turn on Module Logging** is set for a module, the value of the
**LogPipelineExecutionDetails** property of the module is `$true` in all
sessions and it can't be changed.

The "Turn On Module Logging" group policy setting is located in the following
Group Policy paths:

```
Computer Configuration\
  Administrative Templates\
    Windows Components\
     Windows PowerShell

User Configuration\
  Administrative Templates\
    Windows Components\
      Windows PowerShell
```

The User Configuration policy takes precedence over the Computer Configuration
policy, and both policies take preference over the value of the
LogPipelineExecutionDetails property of modules and snap-ins.

For more information about this Group Policy setting, see
[about_Group_Policy_Settings][03].

## Security and Auditing

The Windows PowerShell event log is designed to indicate activity and to
provide operational details for troubleshooting.

However, like most Windows-based application event logs, the Windows PowerShell
event log isn't designed to be secure. It shouldn't be used to audit security
or to record confidential or proprietary information.

Event logs are designed to be read and understood by users. Users can read from
and write to the log. A malicious user could read an event log on a local or
remote computer, record false data, and then prevent the logging of their
activities.

## Notes

Authors of module authors can add logging features to their modules. For more
information, see [Writing a Windows PowerShell Module][02].

## See also

- [about_Group_Policy_Settings][03]
- [about_Preference_Variables][04]
- [Get-EventLog][05]
- [Get-WmiObject][06]

<!-- link references -->
[01]: about_Providers.md
[02]: /powershell/scripting/developer/module/writing-a-windows-powershell-module
[03]: about_Group_Policy_Settings.md
[04]: about_Preference_Variables.md
[05]: xref:Microsoft.PowerShell.Management.Get-EventLog
[06]: xref:Microsoft.PowerShell.Management.Get-WmiObject

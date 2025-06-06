---
description: Describes how to define and use parameter sets in advanced functions.
Locale: en-US
ms.date: 03/26/2025
online version: https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_parameter_sets?view=powershell-5.1&WT.mc_id=ps-gethelp
title: about_Parameter_Sets
---
# about_Parameter_Sets

## Short description

Describes how to define and use parameter sets in advanced functions.

## Long description

PowerShell uses parameter sets to enable you to write a single function that
can do different actions for different scenarios. Parameter sets enable you to
expose different parameters to the user. And, to return different information
based on the parameters specified by the user. You can only use one parameter
set at a time.

## Parameter set requirements

The following requirements apply to all parameter sets.

- If no parameter set is specified for a parameter, the parameter belongs to
  all parameter sets.

- Each parameter set must have a unique combination of parameters. If possible,
  at least one of the unique parameters should be a mandatory parameter.

- A parameter set that contains multiple positional parameters must define
  unique positions for each parameter. No two positional parameters can specify
  the same position.

> [!NOTE]
> There is a limit of 32 parameter sets.

## Default parameter sets

When multiple parameter sets are defined, the `DefaultParameterSetName` keyword
of the **CmdletBinding** attribute specifies the default parameter set.
PowerShell uses the default parameter set when it can't determine the parameter
set to use based on the information provided to the command. For more
information about the **CmdletBinding** attribute, see
[about_Functions_CmdletBindingAttribute][01].

## Declaring parameter sets

To create a parameter set, you must specify the `ParameterSetName` keyword of
the **Parameter** attribute for every parameter in the parameter set. For
parameters that belong to multiple parameter sets, add a **Parameter**
attribute for each parameter set.

The **Parameter** attribute enables you to define the parameter differently for
each parameter set. For example, you can define a parameter as mandatory in one
set and optional in another. However, each parameter set must contain at least
one unique parameter.

Parameters that don't have an assigned parameter set name belong to all
parameter sets.

## Reserved parameter set name

PowerShell reserves the parameter set name `__AllParameterSets` for special
handling.

`__AllParameterSets` is the name of the default parameter set when an explicit
default name is not used.

Setting the `ParameterSetName` of a the **Parameter** attribute to
`__AllParameterSets` is equivalent to not assigning a `ParameterSetName`. In
both cases the parameter belongs to all parameter sets.

> [!NOTE]
> The **CmdletBinding** attribute doesn't prevent you from setting the
> `DefaultParameterSetName` to be to `__AllParameterSets`. If you do this,
> PowerShell creates an explicit parameter set that can't be properly
> referenced by the **Parameter** attribute.

## Examples

The following example function counts the number lines, characters, and words
in a text file. Using parameters, you can specify the values you want returned
and the files you want to measure. There are four parameter sets defined:

- Path
- PathAll
- LiteralPath
- LiteralPathAll

```powershell
function Measure-Lines {
    [CmdletBinding(DefaultParameterSetName = 'Path')]
    param (
        [Parameter(Mandatory, ParameterSetName = 'Path', Position = 0)]
        [Parameter(Mandatory, ParameterSetName = 'PathAll', Position = 0)]
        [string[]]$Path,

        [Parameter(Mandatory, ParameterSetName = 'LiteralPathAll', ValueFromPipeline)]
        [Parameter(Mandatory, ParameterSetName = 'LiteralPath', ValueFromPipeline)]
        [string[]]$LiteralPath,

        [Parameter(ParameterSetName = 'Path')]
        [Parameter(ParameterSetName = 'LiteralPath')]
        [switch]$Lines,

        [Parameter(ParameterSetName = 'Path')]
        [Parameter(ParameterSetName = 'LiteralPath')]
        [switch]$Words,

        [Parameter(ParameterSetName = 'Path')]
        [Parameter(ParameterSetName = 'LiteralPath')]
        [switch]$Characters,

        [Parameter(Mandatory, ParameterSetName = 'PathAll')]
        [Parameter(Mandatory, ParameterSetName = 'LiteralPathAll')]
        [switch]$All,

        [Parameter(ParameterSetName = 'Path')]
        [Parameter(ParameterSetName = 'PathAll')]
        [switch]$Recurse
    )

    begin {
        if ($All) {
            $Lines = $Words = $Characters = $true
        }
        elseif (($Words -eq $false) -and ($Characters -eq $false)) {
            $Lines  = $true
        }
    }
    process {
        if ($Path) {
            $Files = Get-ChildItem -Path $Path -Recurse:$Recurse -File
        }
        else {
            $Files = Get-ChildItem -LiteralPath $LiteralPath -File
        }
        foreach ($file in $Files) {
            $result = [ordered]@{ }
            $result.Add('File', $file.FullName)

            $content = Get-Content -LiteralPath $file.FullName

            if ($Lines) { $result.Add('Lines', $content.Length) }

            if ($Words) {
                $wc = 0
                foreach ($line in $content) { $wc += $line.Split(' ').Length }
                $result.Add('Words', $wc)
            }

            if ($Characters) {
                $cc = 0
                foreach ($line in $content) { $cc += $line.Length }
                $result.Add('Characters', $cc)
            }

            New-Object -TypeName psobject -Property $result
        }
    }
}
```

Each parameter set must have a unique parameter or a unique combination of
parameters. The `Path` and `PathAll` parameter sets are very similar but the
**All** parameter is unique to the `PathAll` parameter set. The same is true
with the `LiteralPath` and `LiteralPathAll` parameter sets. Even though the
`PathAll` and `LiteralPathAll` parameter sets both have the **All** parameter,
the **Path** and **LiteralPath** parameters differentiate them.

Use `Get-Command -Syntax` shows you the syntax of each parameter set. However
it doesn't show the name of the parameter set. The following example shows
which parameters can be used in each parameter set.

```powershell
(Get-Command Measure-Lines).ParameterSets |
  Select-Object -Property @{n='ParameterSetName';e={$_.Name}},
    @{n='Parameters';e={$_.ToString()}}
```

```Output
ParameterSetName Parameters
---------------- ----------
Path             [-Path] <string[]> [-Lines] [-Words] [-Characters] [-Recurse] [<CommonParameters>]
PathAll          [-Path] <string[]> -All [-Recurse] [<CommonParameters>]
LiteralPath      -LiteralPath <string[]> [-Lines] [-Words] [-Characters] [<CommonParameters>]
LiteralPathAll   -LiteralPath <string[]> -All [<CommonParameters>]
```

### Parameter sets in action

The example uses the `PathAll` parameter set.

```powershell
Measure-Lines test* -All
```

```Output
File                       Lines Words Characters
----                       ----- ----- ----------
C:\temp\test\test.help.txt    31   562       2059
C:\temp\test\test.md          30  1527       3224
C:\temp\test\test.ps1          3     3         79
C:\temp\test\test[1].txt      31   562       2059
```

### Error using parameters from multiple sets

In this example, unique parameters from different parameter sets are used.

```powershell
Get-ChildItem -Path $PSHOME -LiteralPath $PSHOME
```

```Output
Get-ChildItem : Parameter set can't be resolved using the specified named parameters.
At line:1 char:1
+ Get-ChildItem -Path $PSHOME -LiteralPath $PSHOME
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidArgument: (:) [Get-ChildItem], ParameterBindingException
    + FullyQualifiedErrorId : AmbiguousParameterSet,Microsoft.PowerShell.Commands.GetChildItemCommand
```

The **Path** and **LiteralPath** parameters are unique to different parameter
sets of the `Get-ChildItem` cmdlet. When the parameters are run together in the
same cmdlet, an error is thrown. Only one parameter set can be used per cmdlet
call at a time.

### How to know which parameter set is used

The automatic variable `$PSCmdlet` provides the **ParameterSetName** property.
This property contains the name of the parameter set being used. You can use
this property in your function to determine which parameter set is being used
to select parameter set-specific behavior.

```powershell
function Get-ParameterSetName {

    [CmdletBinding(DefaultParameterSetName = 'Set1')]
    param (
        [Parameter(ParameterSetName = 'Set1', Position = 0)]
        $Var1,

        [Parameter(ParameterSetName = 'Set2', Position = 0)]
        $Var2,

        [Parameter(ParameterSetName = 'Set1', Position = 1)]
        [Parameter(ParameterSetName = 'Set2', Position = 1)]
        $Var3,

        [Parameter(Position = 2)]
        $Var4
    )

    "Using Parameter set named '$($PSCmdlet.ParameterSetName)'"

    switch ($PSCmdlet.ParameterSetName) {
        'Set1' {
            "`$Var1 = $Var1"
            "`$Var3 = $Var3"
            "`$Var4 = $Var4"
            break
        }
        'Set2' {
            "`$Var2 = $Var2"
            "`$Var3 = $Var3"
            "`$Var4 = $Var4"
            break
        }
    }
}

PS> Get-ParameterSetName 1 2 3

Using Parameter set named 'Set1'
$Var1 = 1
$Var3 = 2
$Var4 = 3

PS> Get-ParameterSetName -Var2 1 2 3

Using Parameter set named 'Set2'
$Var2 = 1
$Var3 = 2
$Var4 = 3
```

<!-- link references -->
[01]: about_Functions_CmdletBindingAttribute.md

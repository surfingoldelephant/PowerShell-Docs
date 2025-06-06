---
external help file: Microsoft.PowerShell.PSReadLine2.dll-Help.xml
Locale: en-US
Module Name: PSReadLine
ms.date: 12/13/2022
online version: https://learn.microsoft.com/powershell/module/psreadline/remove-psreadlinekeyhandler?view=powershell-5.1&WT.mc_id=ps-gethelp
schema: 2.0.0
title: Remove-PSReadLineKeyHandler
---

# Remove-PSReadLineKeyHandler

## SYNOPSIS
Removes a key binding.

## SYNTAX

```
Remove-PSReadLineKeyHandler [-Chord] <String[]> [-ViMode <ViMode>] [<CommonParameters>]
```

## DESCRIPTION

The `Remove-PSReadLineKeyHandler` cmdlet removes a specified key binding.

## EXAMPLES

### Example 1: Remove a binding

```powershell
Remove-PSReadLineKeyHandler -Chord Ctrl+B
```

This command removes the binding from the key combination, or chord, `Ctrl+B`. The `Ctrl+B` chord is
created in the `Set-PSReadLineKeyHandler` article.

## PARAMETERS

### -Chord

Specifies an array of keys or sequences of keys to be removed. A single binding is specified by
using a single string. If the binding is a sequence of keys, separate the keys by a comma, as in
the following example:

`Ctrl+x,Ctrl+l`

This parameter accepts an array of strings. Each string is a separate binding, not a sequence of
keys for a single binding.

```yaml
Type: System.String[]
Parameter Sets: (All)
Aliases: Key

Required: True
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -ViMode

Specify which vi mode the binding applies to. Possible values are: Insert, Command.

```yaml
Type: Microsoft.PowerShell.ViMode
Parameter Sets: (All)
Aliases:
Accepted values: Insert, Command

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### CommonParameters

This cmdlet supports the common parameters: -Debug, -ErrorAction, -ErrorVariable,
-InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose,
-WarningAction, and -WarningVariable. For more information, see
[about_CommonParameters](http://go.microsoft.com/fwlink/?LinkID=113216).

## INPUTS

### None

You can't pipe objects to this cmdlet.

## OUTPUTS

### None

This cmdlet returns no output.

## NOTES

## RELATED LINKS

[Get-PSReadLineKeyHandler](Get-PSReadLineKeyHandler.md)

[Get-PSReadLineOption](Get-PSReadLineOption.md)

[Set-PSReadLineOption](Set-PSReadLineOption.md)

[Set-PSReadLineKeyHandler](Set-PSReadLineKeyHandler.md)

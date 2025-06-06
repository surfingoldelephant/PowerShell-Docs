---
description: Describes the operators that work with Microsoft .NET types.
Locale: en-US
ms.date: 08/29/2022
no-loc: [-as]
online version: https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_type_operators?view=powershell-7.4&WT.mc_id=ps-gethelp
schema: 2.0.0
title: about_Type_Operators
---
# about_Type_Operators

## Short description
Describes the operators that work with Microsoft .NET types.

## Long description

The Boolean type operators (`-is` and `-isnot`) tell whether an object is an
instance of a specified .NET type. The `-is` operator returns a value of
**TRUE** if the type matches and a value of **FALSE** otherwise. The `-isnot`
operator returns a value of **FALSE** if the type matches and a value of
**TRUE** otherwise.

The `-as` operator tries to convert the input object to the specified .NET
type. If it succeeds, it returns the converted object. If it fails, it returns
`$null`. It does not return an error.

PowerShell has the  following type operators:

- `-is`   |Returns TRUE when the input is an instance of the specified .NET type.

  ```powershell
  (Get-Date) -is [datetime]     # Result is True
  ```

- `-isnot`|Returns TRUE when the input not an instance of the specified.NET type.

  ```powershell
  (Get-Date) -isnot [datetime]  # Result is False
  ```

- `-as`   |Converts the input to the   specified .NET type.

  ```powershell
  "5/7/07" -as [datetime]       # Result is Monday, May 7, 2007 12:00:00 AM
  ```

The syntax of the type operators is as follows:

```powershell
<input> <operator> [.NET type]
```

You can also use the following syntax:

```powershell
<input> <operator> ".NET type"
```

The .NET type can be written as a type name in brackets or a string, such as
`[datetime]` or `"DateTime"` for **System.DateTime**. If the type is not at the
root of the system namespace, specify the full name of the object type. You can
omit "System.". For example, to specify **System.Diagnostics.Process**, enter
`[System.Diagnostics.Process]`, `[Diagnostics.Process]`, or
`"Diagnostics.Process"`.

The type operators always operate on the input object as a whole. That is, if
the input object is a collection, it is the _collection_ type that is tested,
not the types of the collection's _elements_.

### -is/isnot operators

The **Boolean** type operators (`-is` and `-isnot`) always return a **Boolean**
value, even if the input is a collection of objects.

If `<input>` is a type that is the same as or is _derived_ from the .NET Type,
the `-is` operator returns `$true`.

For example, the **DirectoryInfo** type is derived from the **FileSystemInfo**
type. Therefore, both of these examples return **True**.

```powershell
PS> (Get-Item /) -is [System.IO.DirectoryInfo]
True
PS> (Get-Item /) -is [System.IO.FileSystemInfo]
True
```

The `-is` operator can also match interfaces if the `<input>` implements the
interface in the comparison. In this example, the input is an array. Arrays
implement the **System.Collections.IList** interface.

```powershell
PS> 1, 2 -is [System.Collections.IList]
True
```

### -as operator

The `-as` operator tries to convert the input object to the specified .NET
type. If it succeeds, it returns the converted object. It if fails, it returns
`$null`. It does not return an error.

If the `<input>` is a type that is _derived_ from the .NET Type `-as` _passes
through_ returns input object unchanged. For example, the **DirectoryInfo**
type is derived from the **FileSystemInfo** type. Therefore, the object type is
unchanged in the following example:

```powershell
PS> $fsroot = (Get-Item /) -as [System.IO.FileSystemInfo]
PS> $fsroot.GetType().FullName
System.IO.DirectoryInfo
```

#### Converting the DateTime type is culture-sensitive

Unlike type casting, converting to `[datetime]` type using the `-as` operator
only works with strings that are formatted according to the rules of the
current culture.

```powershell
PS> [cultureinfo]::CurrentCulture = 'fr-FR'
PS> '13/5/20' -as [datetime]

mercredi 13 mai 2020 00:00:00

PS> '05/13/20' -as [datetime]
PS> [datetime]'05/13/20'

mercredi 13 mai 2020 00:00:00

PS> [datetime]'13/05/20'
InvalidArgument: Cannot convert value "13/05/20" to type "System.DateTime".
Error: "String '13/05/20' was not recognized as a valid DateTime."
```

To find the .NET type of an object, use the `Get-Member` cmdlet. Or, use the
**GetType** method of all the objects together with the **FullName** property
of this method. For example, the following statement gets the type of the
return value of a `Get-Culture` command:

```powershell
PS> (Get-Culture).GetType().FullName
System.Globalization.CultureInfo
```

## Examples

The following examples show some uses of the Type operators:

```powershell
PS> 32 -is [float]
False

PS> 32 -is "int"
True

PS> (Get-Date) -is [datetime]
True

PS> "12/31/2007" -is [datetime]
False

PS> "12/31/2007" -is [string]
True

PS> (Get-Process powershell)[0] -is [System.Diagnostics.Process]
True

PS> (Get-Command Get-Member) -is [System.Management.Automation.CmdletInfo]
True
```

The following example shows that when the input is a collection of objects, the
matching type is the .NET type of the collection, not the type of the
individual objects in the collection.

In this example, although both the `Get-Culture` and `Get-UICulture` cmdlets
return **System.Globalization.CultureInfo** objects, a collection of these
objects is a System.Object array.

```powershell
PS> (Get-Culture) -is [System.Globalization.CultureInfo]
True

PS> (Get-UICulture) -is [System.Globalization.CultureInfo]
True

PS> (Get-Culture), (Get-UICulture) -is [System.Globalization.CultureInfo]
False

PS> (Get-Culture), (Get-UICulture) -is [array]
True

PS> (Get-Culture), (Get-UICulture) | foreach {
  $_ -is [System.Globalization.CultureInfo])
}
True
True

PS> (Get-Culture), (Get-UICulture) -is [Object]
True
```

The following examples show how to use the `-as` operator.

```powershell
PS> "12/31/07" -is [datetime]
False

PS> "12/31/07" -as [datetime]
Monday, December 31, 2007 12:00:00 AM

PS> $date = "12/31/07" -as [datetime]

C:\PS>$a -is [datetime]
True

PS> 1031 -as [System.Globalization.CultureInfo]

LCID      Name      DisplayName
----      ----      -----------
1031      de-DE     German (Germany)
```

The following example shows that when the `-as` operator cannot convert the
input object to the .NET type, it returns `$null`.

```powershell
PS> 1031 -as [System.Diagnostics.Process]
PS>
```

## See also

- [about_Operators](about_Operators.md)

# AX 2012 R3 DIXF/DMF Error After Migrating to SQL Server 2016 SSIS

## Overview

This repository documents a real-world troubleshooting scenario encountered while migrating a Microsoft Dynamics AX 2012 R3 Data Import Export Framework (DIXF/DMF) environment from SQL Server 2014 SSIS to SQL Server 2016 SSIS.

The error message initially pointed to a missing SSIS assembly, but the actual root cause was a version mismatch between the installed DMF components and the AX environment.

---

## Environment

### Dynamics AX

```text
Microsoft Dynamics AX 2012 R3
Kernel Version: 6.3.3000.2948
```

### SQL Server

```text
SQL Server 2016
SSIS Version 13.0
DTExec Version 13.0.6300.2
```

---

## Symptoms

When attempting to preview a source file in a DIXF Processing Group:

```text
Could not load file or assembly
'Microsoft.SqlServer.ManagedDTS, Version=10.0.0.0'
or one of its dependencies.
The system cannot find the file specified.
```

---

## Additional Issue Found

During troubleshooting:

```text
Could not find a part of the path
C:\Program Files\Microsoft Dynamics AX\60\SetupSupport\AxSetup.config
```

Root cause:

```text
AX Setup Support installation was corrupted.
```

Resolution:

```text
Reinstalled SetupSupport64.msi
```

---

## Investigation

The following were verified:

### DTExec

```cmd
where dtexec
```

Output:

```text
C:\Program Files (x86)\Microsoft SQL Server\130\DTS\Binn\DTExec.exe
```

### Version

```cmd
dtexec /?
```

Output:

```text
Version 13.0.6300.2
```

### ManagedDTS

```text
Microsoft.SqlServer.ManagedDTS
Version 13.0.0.0
```

was confirmed in the GAC.

---

## Root Cause

The SQL Server installation was healthy.

The actual issue was that the installed DMF components were at an older version level and were still attempting to load legacy SSIS assemblies.

The environment was running:

```text
AX Kernel 6.3.3000.2948
```

while the DMF components were significantly older.

This resulted in DIXF requesting:

```text
Microsoft.SqlServer.ManagedDTS
Version=10.0.0.0
```

which corresponds to older SQL Server SSIS components.

---

## Resolution

Upgraded the DMF installation on the database server to:

```text
6.3.6000.9705
```

After upgrading:

- DMF Services installed successfully
- Preview Source File worked correctly
- DIXF imports executed successfully
- SQL Server 2016 SSIS worked normally

---

## Lessons Learned

A ManagedDTS error does not necessarily indicate a missing SSIS installation.

Always validate:

- AX Kernel version
- DMF version
- DTExec version
- SSIS installation
- ManagedDTS assembly version

Version mismatches between DMF and AX can generate misleading assembly loading errors.

---

## Keywords

Dynamics AX 2012 R3

DIXF

DMF

SQL Server 2016

SSIS

ManagedDTS

Data Import Export Framework

Microsoft.SqlServer.ManagedDTS

Preview Source File
## Author

**Ashok Sathyanarayan**  
Microsoft Dynamics AX / Dynamics 365 Finance & Operations Technical Consultant

## About the Author

Experienced ERP consultant specializing in Microsoft Dynamics AX 2012 and Dynamics 365 Finance & Operations, with expertise in troubleshooting, performance optimization, integrations, data migration, and production support.

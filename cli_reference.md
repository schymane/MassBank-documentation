# Command line tool reference

## Validator
A tool to validate the content of one or several record files. Validation checks compliance with [MassBank record format specification](https://github.com/MassBank/MassBank-web/blob/main/Documentation/MassBankRecordFormat.md)
and also checks for serialisation problems from record file content to data structure back to record file content.

Usage: 
```
Validator [OPTIONS] <FILE|DIR> [<FILE|DIR> ...]
 <FILE|DIR>		a record file or a directory with record files(scanned recursively)
 -legacy			do validation in legacy mode, is less strict and is not allowed for new records.
 -db					DEVELOPER FREATURE. After validation and serialisation tests, record is retrieved from database and converted
 							back to string and compared with original content. Requires a working MassBank MariaDB container.

```

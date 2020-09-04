# User Documentation for MassBank

## Conversion 

In this section we collect pointers to existing software implementations that read / write / process MassBank records. 

### MassBank and Bruker `*.library` conversion

This command line converter https://github.com/MassBank/MassBank2NIST has been developed (despite its name) to convert bi-directional between MassBank records and the NIST-like Bruker `*.library` format.

### MassBank to NIST MSP conversion

The computational mass spectrometry team has a Windows GUI converter to create `*.msp` files from MassBank records:
http://prime.psc.riken.jp/compms/others/main.html#Massbank2msp
Afterwards you can use `LIB2Nist` to create to the (binary) NIST library format is used by different programs.

### MassBank to NIST/RIKEN MSP conversion

With the MassBank-web software we provide a cli tool `RecordExporter`. This software can convert MassBank records to different `msp` formats. The tool processes an arbitrary number of files specified on command line either directly or found recursively in the specified directories. All records are written in one single `msp` in the format specified by the format option.

Usage: 
```
RecordExporter [OPTIONS] <FILE|DIR> [<FILE|DIR> ...]
 -f,--format <arg>    output format; possible values: RIKEN_MSP, NIST_MSP;
                      default is RIKEN_MSP
 -o,--outfile <arg>   name of output file

```


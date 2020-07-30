# User Documentation for MassBank

## Conversion 

In this section we collect pointers to existing software implementations that read / write / process MassBank records. 

### MassBank and Bruker `*.library` conversion

This command line converter https://github.com/MassBank/MassBank2NIST has been developed (despite its name) to convert bi-directional between MassBank records and the NIST-like Bruker `*.library` format.

### MassBank to NIST MSP conversion

The computational mass spectrometry team has a Windows GUI converter to create `*.msp` files from MassBank records:
http://prime.psc.riken.jp/compms/others/main.html#Massbank2msp
Afterwards you can use `LIB2Nist` to create to the (binary) NIST library format is used by different programs.

# Contributor documentation

Contributors are welcome to provide new spectra to MassBank. New spectra should meet the format described in the [MassBank record format specification](https://github.com/MassBank/MassBank-web/blob/master/Documentation/MassBankRecordFormat.md). All MassBank spectra are stored in the [MassBank-data](https://github.com/MassBank/MassBank-data) repository. To submit new spectra create a pull request to MassBank-data or open an issue to get in contact.

## Generation of new records for upload to MassBank-data
The preferred software to generate MassBank records is [RMassBank](https://www.bioconductor.org/packages/release/bioc/html/RMassBank.html). RMassBank is in continuous development. Hence, use the latest version available, please. If you are using your own pipeline please consider to provide it to other users(please open an issue).

## Validate new record files locally
Before new record files are accepted to MassBank-data, they need to pass the validation. This will be done automatically if a pull request is created. Its also possible to validate record files locally in advance. To validate new record files locally please clone this repo. Under the `.scripts` directory create the source tree of the [MassBank-web](https://github.com/MassBank/MassBank-web) project and compile it, because the [Validator](https://github.com/MassBank/MassBank-web/blob/master/MassBank-Project/MassBank-lib/src/main/java/massbank/Validator.java) program is needed.
You can use the following commands:
```
git clone --depth 1 -q https://github.com/MassBank/MassBank-web.git .scripts/MassBank-web
mvn -q -f .scripts/MassBank-web/MassBank-Project/MassBank-lib/pom.xml install
```
Now its possible to validate record files locally. `validate.sh` supports validation of a single record file, several files as well as one or several directories containing record files, eg. 
```
./.scripts/validate.sh AAFC
```
To validate the full date repository use the `validate_all.sh` script.

## Submit new records to MassBank-data or modify existing records
To submit data or to change existing records:
1. Please fork the [MassBank-data](https://github.com/MassBank/MassBank-data) repo, clone it to your place and make your changes or add new content in the dev branch.
2. Commit and push your changes to the dev branch of your repo.
3. Create a pull request against the dev branch.

Your pull request will be validated and you will receive a report whether your records meet the [MassBank specs](https://github.com/MassBank/MassBank-web/blob/master/Documentation/MassBankRecordFormat.md). If everything is ok, your content will be integrated into MassBank.
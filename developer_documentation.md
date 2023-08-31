# Installation of MassBank
MassBank needs a MariaDB database running somewhere, a tomcat server for the webapp and the maven build system for installation. This can be provided directly by the hosting system or in docker containers. Although in principle it might be possible to run this webapp on a windows server, we haven't tested it and it will not run out of the box.

Besides running MassBank on a server system it is also possible to run it in the integrated tomcat server within eclipse for easy development and debugging. Our development platform is Ubuntu 18.04.

## Install in eclipse for development
Needed for a working development environment:
1. MySQL database - we use a MariaDB docker container
2. Eclipse Java EE IDE for Web Developers.
3. Local Apache tomcat for eclipse

### Install MariaDB docker
Install and configure [docker environment](https://docs.docker.com/install/linux/docker-ce/ubuntu/) including [docker-compose](https://docs.docker.com/compose/install/). Make sure that your user is in the group 'docker' and adjust your root password for the database in `compose/mariadb-docker-compose.yml`.

Start the database with `docker-compose -f compose/mariadb-docker-compose.yml up -d` and check with `docker ps` for **massbank_mariadb**. Check database connectivity with `mysql -u root -h 127.0.0.1 -p`.

### Install massbank.conf
Adjust settings in massbank.conf and copy it to /etc. 

### Import project into Eclipse
Download and install the [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads). Start eclipse and import the folder 'MassBank-Project' from this repo: File -> Import -> Existing Maven Project -> Select this repo for import. 
Download [Apache Tomcat](http://tomcat.apache.org/) and extract it to your preferred folder.
Then create a Tomcat server in eclipse. 

### Populate database
Before the MassBank webapp can serve the data the database needs to be filled.

First otion is to clone the data from [MassBank-data](https://github.com/MassBank/MassBank-data) to a local directory and make sure the 'DataRootPath' in 'massbank.conf' is correctly set to your data repo. Run '/MassBank-lib/src/main/java/massbank/RefreshDatabase.java' from to populate your database. This way a `dev`-snapshot of the data can be installed.

Second option is to download the sql dump from the latest release and import it into the database with `mysql -u root -h 127.0.0.1 -p < MassBank-2020.10.sql`.

### Run MassBank webapp
Run the MassBank-project on the Tomcat server and access MassBank at [http://localhost:8080/MassBank/](http://localhost:8080/MassBank/).

## Install as server system with docker/multiple instances on one server possible
A docker compose file named `compose/full-service.yaml` is provided to install all required container to run MassBank as a server system. The only requirement is the  [docker environment](https://docs.docker.com/install/linux/docker-ce/ubuntu/) including [docker-compose](https://docs.docker.com/compose/install/). Make sure that your user is in the group 'docker'. A setup script called `install.sh` is provided to make deployment easy.

### Install one instance with `docker-compose`
To set up the MassBank webapp the source of the webapp and the data repository are needed. Get them with:
```
git clone git@github.com:MassBank/MassBank-web.git
git clone git@github.com:MassBank/MassBank-data.git
```
If you want to use the `install.sh`-script from MassBank-web both repositories need to be in the same directory. Usage is:
```
./install.sh 
Usage: install.sh <operation> <instance>
         <operation> ... start, stop, deploy or refresh
         <instance>  ... 0 to 9
```
`install.sh` supports four operations:
- start a MassBank service (incl. the required database)
- stop and remove a MassBank service
- refresh the content of the MassBank
- deploy the code to the tomcat container
- instance is used to separate the networks of different MassBank servers and determines the port of the service, 0 serves at 8080, 1 serves at 8081 ...

Several networks with independent instances of MassBank are possible with this deployment method. To deploy to standard port 8080 use `./install.sh start 0`. After 30 min you can find a MassBank instance at http://\<your-ip\>:8080/MassBank. To upload new content update the data repo (needs to be in `../MassBank-data`) and issue `./install.sh refresh 0`. To remove this MassBank use `./install.sh stop 0`.

### Install multiple instance with `docker-compose`
The deployment uses the codebase in MassBank-web and the data in MassBank-data in the same parent directory for one deployment. Because the data in `MassBank-data` is not only used for deployment, but also for serving it is recommended to have a separate data repo for each instance of MassBank, e.g.:
```
|
|-MassBank1
|  |
|  |-MassBank-web
|  |-MassBank-data
|
|-MassBank2
   |
   |-MassBank-web 
   |-MassBank-data
```
With this layout its easy to have several instances with different codebase / data on the same server. To start MassBank1 go to MassBank1/MassBank-web and use `./install.sh start 1`. Your server will be at http://\<your-ip\>:8081/MassBank. To start MassBank2 go to MassBank2/MassBank-web and use `./install.sh start 2`. Your server will be at http://\<your-ip\>:8082/MassBank. The second parameter of the install script is used to separate the different instances by using different subnets and ports on the local machine. 

## Install as server system with Vagrant
A `Vagrantfile` is provided for easy installation of a MassBank-server. This config creates a Ubuntu VM with IP `192.168.35.18`. Inside this VM the `docker-compose` mechanism as described above is used to create a MassBank-server on port 8080. Additionally a Apache2 http server is installed as reverse proxy. The config can be found in `conf/apache2`. Please modify if needed. The final MassBank site will be available at [https://192.168.35.18/MassBank](https://192.168.35.18/MassBank/). The installation uses the MassBank-data repository from `../MassBank-data`. You can modify the location in the Vagrantfile. The installation can be started with `vagrant up`.

## Sitemaps, robots and scrapers
MassBank will serve a [sitemap index](https://massbank.eu//MassBank/sitemapindex.xml) which points to the actual sitemaps containing links to the individual [sitempas](https://massbank.eu/MassBank/sitemap/sitemap1.xml) with a maximum of 50k entries each. These sitemaps can be used by search engines or for semantic data extraction. 

## PIWIK log analyser (https://piwik.org/)
The default MassBank server installation includes the PIWIK log analyser. Consider that user tracking has privacy issues.
The default preset follows very strict rules according to http://piwik.org/docs/privacy/ and only the following usage data:

* Site from which the file was requested
* Name of the file
* Date and time of the request
* Volume of data transferred
* Access status (file transferred, file not found)
* Description of the type of web browser used
* IP address of the requesting computer shortened by the last six digits.

The creation of personal user profiles is therefore precluded.

## customised piwik tracking code

The tracking code for piwik is specific for each site and configuration in order to fullfil your local legal and personal requirements!
The tracking code for http://your_server_url/MassBank is called from /var/www/html/MassBank/script/Piwik.js.
Make sure to replace the default code with your customised tracking code. Exclude the script tags.
Make also sure that you customise your superuser if using the default bootstrap.sh!

# Release strategy of MassBank-web

## Branches
We use two branches, `main` and `dev`. All development should happen in `dev` or a feature branch and we define every major commit to `main` to be a release. When the source code in the `dev` branch reaches a stable point and is ready to be released, all of the changes should be merged into `main` and then tagged with a release number. How this is done in detail will be discussed further on. To use all of the command lines below the [github/hub](https://hub.github.com/) tool is required.

### Release branches
Branch off from: `dev`

Branch naming: `release-*`

Release branches support preparation of a new production release. They allow for minor bug fixes and preparing the version number for a release. It is exactly at the start of a release branch that the upcoming release gets assigned a version number.

#### Creating a release branch
```
$ git checkout -b release-2.1 dev
Switched to a new branch "release-2.1"
$ ./bump-version.sh 2.1
$ git add *pom.xml
$ git commit -m "Bumped version number to 2.1"
[release-2.1 74d9424] Bumped version number to 2.1
$ git push --set-upstream origin release-2.1
```

#### Finishing a release branch
When the state of the release branch is ready to become a real release, the release branch is merged into `main` with a pull request and tagged for easy future reference.

```
$ hub pull-request -m 'Release version 2.1' -b main
```
Wait for all checks to finish.
```
$ git pull origin main
$ git checkout main
$ git merge release-2.2.4
```
Resolve conflicts, commit and push.

If this is done, the release can be merged to `main`. 
```
$ git push -u origin main
```

Its adviced to merge the changes of the `release/main` branch back to `dev`.
```
$ git checkout dev
$ git merge main
```

Set a new version number to the `dev` branch.
```
$ ./bump-version.sh 2.1.1-SNAPSHOT
$ git add *pom.xml
$ git commit -m "Bumped version number to 2.1"
git push
```

Now we are done and the release branch may be removed.
```
$ git branch -d release-2.1
Deleted branch release-2.1 (was ff452fe).
$ git push origin --delete release-2.1
```

### Creating a release
After the merge to the main branch a tag and release needs to be created.
```
$ git checkout main
$ git tag -a "2.1" -m "tag release 2.1"
$ git push origin 2.1
$ hub release create 2.1
```

# Release process of [MassBank-data](https://github.com/MassBank/MassBank-data)
The release strategy of MassBank-data is similar to the one of MassBank-web and is described below. Additionally it needs to be taken care of the validation and the codebase for the validation.

## Main branches
We use two main branches, `main` and `dev`. All development should happen in `dev` and we define every commit to `main` to be a release. When new data in the `dev` branch has reached a stable point and is ready to be released, all of the changes should be merged back into `main` somehow and then tagged with a release number. The data of `main` should be validated with the `main` of MassBank-web and the data of `dev` should be validated with `dev` of MassBank-web. If the data contains incompatible changes MassBank-web needs to be released first, because before a merge to `main` is possible a validation needs to be successfully. The code base for the validation is defined in travis.yml. Depending on the given version the `bump-version.sh` script adjusts the content of travis.yml to choose the corresponding branch from MassBank-web. If the version tag contains `SNAPSHOT` like in `2019.09-SNAPSHOT` its identified as `dev` data branch and travis will pull the `dev` branch of MassBank-web for validation. If `SNAPSHOT` is missing its identified to be a release version and travis will pull the `main` branch of MassBank-web for validation. All versions which get a release tag in github are used by a webhook from zenodo and get a DOI attached. The authors list of the record at zenodo needs to be manually edited to contain `MassBank-consortium and its contributors`.

The release process is discussed in detail below. To use all of the command lines below the [github/hub](https://github.com/github/hub) tool is required.


### Release branches
Release branches are created in preparation of a release.

Branch off from: `dev`

Must merge back into: `main`

Branch naming: `release-*`

Release branches support preparation of a new production release. They allow for preparing the version number for a release.

#### Creating a release branch
```
$ git checkout -b release-2019.09 dev
Switched to a new branch "release-2019.09"
$ ./bump-version.sh 2019.09
Set version of release to 2019.09. This version will validate against the main branch of MassBank-web.
git add .travis.yml VERSION
git commit -m "Bumped version number to 2019.09"
[release-2019.09 74d9424] Bumped version number to 2019.09
$ git push --set-upstream origin release-2019.09
```
#### Finishing a release branch
When the state of the release branch is ready to become a real release, the release branch is merged into `main` with a pull request and tagged for easy future reference.

```
$ hub pull-request -m 'Release version 2019.09' -b main
```
Wait for all checks to finish. If all tests are successfull the release can be merged to `main` and tagged as release.
```
git merge main
```
Resolve conflicts and commit.
```
$ git checkout main
$ git merge --no-ff release-2019.09
$ git push origin main
$ hub release create -m 'Release version 2019.09' 2019.09
```
Now we are done and the release branch may be removed
```
$ git branch -d release-2019.09
Deleted branch release-2019.09 (was ff452fe).
```
and the version of the `dev` branch should be incremented.
```
$ git checkout dev
$ ./bump-version.sh 2019.09.1-SNAPSHOT
Set version of release to 2019.09.1-SNAPSHOT. This version will validate against the dev branch of MassBank-web.
git commit -a -m "Bumped version number to 2019.09.1-SNAPSHOT"
[dev 74d9424] Bumped version number to 2019.09.1-SNAPSHOT
$ git push
```

# Complete CI/CD in jenkins for Java APP
## Tools Used in This project
* Jenkins
* Docker
* Amazon EC2
* GIT
## Project Description
* Configure web server on digitalOcean with appropriate firewall configuration
* Deploy Jenkins container on the server
## Add Dockerfile to application to create docker image from the app

## Create a multibranch pipeline for the application
### Jenkinsfile contains the following steps
#### Application version increment
* Application patch version in the pom.xml file is dynamically incremented using mvn build-heper
* The incremented version is read using readFile
* Incremented version is saved as environmental variable with the build number
            
          mvn build-helper:parse-version versions:set \
         -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion}versions:commit

#### Build Application artifact
 * Application is packaged into artifact and the target folder is created automatically 
 * Because the application version will be dynamically increased any time a build is triggered the old artifact will be deleted
              mvn clean package

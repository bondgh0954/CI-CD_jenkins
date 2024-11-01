# Complete CI/CD in jenkins for Java APP
## Tools Used in This project
* Jenkins
* Docker
* Amazon EC2
* GIT
## Project Description
* Configure web server on digitalOcean with appropriate firewall configuration
* Install docker on the server and Deploy Jenkins container on the server
  
          docker run -d -p 8080:8080 -p 50000:50000 -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
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
#### Build Image
* application is build into docker image with its dependencies from the Dockerfile and push into a private docker registry
* Jenkins needs authentication before it can push the image into the registry so github credentials is created in Jenkins and
* used in the pipeline to give jenkins permission to push the image into the registry
* The incremented version is used as image tag

        docker build -t nanaot/java-app:$IMAGE_VERSION
        docker push nanaot/java-app:$IMAGE_VERSION

#### Deploy APP
* Application is deployed on Amazon EC2 server
* The key pair of the server is used to create credential in Jenkins
* Docker composed file is created in the app with the application image and postgress container
* A script to start the docker compose file is created
* Both the docker compose file and script are coppied to the ec2 server from jenkins
* ssh into the ec2 instance and start the docker compose file
  
          ssh -o StrickHostKeyChecking=0 ec2-user@publicIP

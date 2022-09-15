# Build CI CD pipeline with Jenkins

## Github Credentials in Jenkins
### 1. Go to your Github, click user icon and choose settings
![getting-started](/images/7.png)

### 2. Click Developer settings
![getting-started](/images/8.png)

### 3. Choose Personal access tokens
### 4. Generate new token
![getting-started](/images/9.png)
![getting-started](/images/10.png)

- Note = your token name
- Expiration = you token duration
- Select scopes = check the required scope (repo)

### 5. Save your token because it can only be seen once. We will use this token instead of password for our Github Credential in Jenkins.
![getting-started](/images/11.png)

### 6. Go to Jenkins Homepage, click "Manage Jenkins"
![getting-started](/images/12.png)

### 7. Click "Manage credentials"
![getting-started](/images/13.png)

### 8. Click "global" under Stores scoped to Jenkins
![getting-started](/images/14.png)

### 9. Clid "Add Credentials"
![getting-started](/images/15.png)
![getting-started](/images/16.png)

Fill in your git username and password (using personal access token from GitHub). Fill your ID (exp: github-login) and Description.

![getting-started](/images/17.png)

Now we have new credentials. Use ID for current project


## **Create repository in docker hub**
Login to your https://hub.docker.com/ and create new repository

![getting-started](/images/25.png)
![getting-started](/images/26.png)

## **Create a pipeline job**
### 1. Open the web browser and open localhost:8080
### 2. The **Jenkins dashboard** that opens, **create new jobs** there
### 3. Select and define what Jenkins job that to be created
![getting-started](/images/1.png)
### 4. Select **Pipeline**, give it a name and click **OK**
![getting-started](/images/2.png)
### 5. Create parameter for your docker acccount password.
![getting-started](/images/3.png)

Checklist `The project is paramaterized` and click `Add Parameter`. Choose `Password Parameter`. Fill the information needed.
![getting-started](/images/4.png)

### 6. Set Build Triggers
Check "Poll SCM" to polls the SCM periodically for checking if any changes/ new commits were made and shall build the project if any new commits were pushed since the last build.

![getting-started](/images/24.png)

- H * * * * = check every hour


### 7. Create pipeline script 
In this guide, we will create pipeline to create image from our Dockerfile in SCM and push it to docker registry. Open your text editor(ex: VS Code), and start write your pipeline script.

``` bash
pipeline {
    agent any
    stages {
        stage('SCM Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-login', url: 'https://github.com/Yuzyzy88/carManagementDashboad.git']]])
            }
        }
    } 
}      
```

At the first steps, we want to do SCM Checkout or Git Pull from our SCM. The command will be like above for pull our SCM. Fill the information needed, in this case :
- branches : main -> pull from main branch.
- credentialsId : github-login
- url : https://github.com/Yuzyzy88/carManagementDashboad.git -> our project url in SCM

``` bash
pipeline {
    agent any
    stages {
        stage('SCM Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-login', url: 'https://github.com/Yuzyzy88/carManagementDashboad.git']]])
            }
        }
         stage('Create Image') {
            steps {
                sh '''
                docker image rm sulistiowatiayu/first-trial:v1 || echo "No existing image found"
                docker build --no-cache -t sulistiowatiayu/first-trial:v1 . 
                '''
            }
        }
    }
}
```

Add the next stage to create our image using docker command. Give the tag for our image.

``` bash 
pipeline {
    agent any
    stages {
        stage('SCM Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-login', url: 'https://github.com/Yuzyzy88/carManagementDashboad.git']]])
            }
        }
        stage('Create Image') {
            steps {
                sh '''
                docker image rm sulistiowatiayu/first-trial:v1 || echo "No existing image found"
                docker build --no-cache -t sulistiowatiayu/first-trial:v1 . 
                '''
            }
        }
        stage('Push Image') {
            steps {
                sh '''
                set +x
                docker login --username=sulistiowatiayu --password=$dockerPassword
                set -x
                docker push sulistiowatiayu/first-trial:v1
                '''
            }
        }
    }
}
```
Add the next stage to push image to our docker hub. First, we need to login to docker, add set +x and set -x so your password doesn't shown in console Jenkins. Push the image with docker push command.

``` bash 
pipeline {
    agent any
    stages {
        stage('SCM Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-login', url: 'https://github.com/Yuzyzy88/carManagementDashboad.git']]])
            }
        }
        stage('Create Image') {
            steps {
                sh '''
                docker image rm sulistiowatiayu/first-trial:v1 || echo "No existing image found"
                docker build --no-cache -t sulistiowatiayu/first-trial:v1 . 
                '''
            }
        }
        stage('Push Image') {
            steps {
                sh '''
                set +x
                docker login --username=sulistiowatiayu --password=$dockerPassword
                set -x
                docker push sulistiowatiayu/first-trial:v1
                '''
            }
        }
        stage('Deploy Image') {
            steps {
                sh '''
                (docker stop carmanagment && docker rm carmanagment) || echo "No existing container running";
                docker run -p 3000:80 -d --name carmanagment sulistiowatiayu/first-trial:v1
                '''
            }
        }
    }
}
```

The last stage is stop and delete the existing container before we run new container.

### 8. Insert pipeline script to Jenkins
There is two way to insert our pipeline script to Jenkins.
![getting-started](/images/5.png)

**A. Pipeline script or write in on Jenkins**
![getting-started](/images/6.png)

**B. Pipeline script from SCM (ex: GitHub)**
Save pipeline script into your git project and name it with `JenkinsFile`.

![getting-started](/images/18.png)
- Repository URL = your GitHub project URL.
- Credentials = your credential to login to SCM.
- Branch to build = used branch
- Script path = fill with Jenkinsfile.

### 9. Save the configuration

### 10. Run your pipeline
![getting-started](/images/19.png)
![getting-started](/images/20.png)

### 11. Monitor your pipeline

![getting-started](/images/21.png)
to check the build history, you can click the number and Select `Console Output`

![getting-started](/images/22.png)
![getting-started](/images/23.png)


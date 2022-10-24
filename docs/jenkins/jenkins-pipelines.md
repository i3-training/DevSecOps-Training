# **Script Jenkinsfile**

![dockerfile-before](/images/full-pipeline.png)
 
# SonarQube Pipeline

SonarQube is an excellent tool for measuring code quality, using static analysis to find code smells, bugs, vulnerabilities, and poor test coverage. Rather than manually analysing the reports, we can automate the process by integrating SonarQube with your Jenkins continuous integration pipeline. This way, you can configure a quality gate based on your own requirements, ensuring bad code always fails the build.

`waitForQualityGate`: Wait for SonarQube analysis to be completed and return quality gate status

This step pauses Pipeline execution and wait for previously submitted SonarQube analysis to be completed and returns quality gate status. Setting the parameter abortPipeline to true will abort the pipeline if quality gate status is not green.

Example using simple declarative pipeline:

```bash
        stage('Sonnar-Scanner') {
                steps {
                        sh '''
                        "your-project-directory"
                        "your-sonarscanner-directory"
                        -Dsonar.projectKey="your-key" \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000/ \
                        -Dsonar.login="your-token"
                        -Dsonar.qualitygate.wait=true
                        '''
                        }
```

Here we give you the pipeline we use in this project :

```bash
        stage('Sonnar-Scanner') {
                steps {
                        sh '''
                        cd app/
                        /home/jenkins/sonar-scanner/sonar-scanner/bin/sonar-scanner \
                        -Dsonar.projectKey="jenkins-devsecops" \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://10.8.60.123:9000/ \
                        -Dsonar.login="e1f735b437dd77262312033dfa83766cdbc3508c" \
                        -Dsonar.qualitygate.wait=true
                        '''
                        }
```

Note: We can use that pipeline for condition that we don't need to build your configuration in sonar-project.properties, so we build the configuration in Jenkins pipeline.

The different condition is when you have your own sonar-project.properties in your project. In this case, you just need one line to configuration in Jenkins pipeline.

Example using simple one line declarative pipeline:

```bash
        stage('Sonnar-Scanner') {
                steps {
                        sh '''
                        "your-project-directory"
                        "your-sonarscanner-directory"
                        '''
                        }
```

Here we give you the pipeline we use in this project :

```bash
        stage('Sonnar-Scanner') {
                steps {
                        sh '''
                        cd app/
                        /home/jenkins/sonar-scanner/sonar-scanner/bin/sonar-scanner
                        '''
                        }
```

# Trivy Pipeline

We can implement Trivy as a DevSecOps tool to find vulnerabilities in existing Dockerfile configurations and images. We can automate the trivy
using jenkins.

In this pipeline, we use a misconfiguration scanner to check for vulnerabilities in the Dockerfile so that if a vulnerability is found, the Jenkins automation process will not proceed to the create image step and will fail the pipeline.

Then the Filesystem scanner will be used to check the vulnerability of modules or libraries that will be used later by the application. In this case, the gossip app built using the Python programming language will have a requierement.txt. Trivy will check whether there are vulnerabilities in the modules and libraries used.

Vulnerability and Secret Scanner will check for vulnerabilities after the image has been created. Because we know for sure that the image will not be possible to create from zero. So it will definitely use the existing base image. Therefore, this step is important because it will check before being pushed to the private registry. Trivy can also do a secret scanner, if there is a secret that is embedded as an env or a file in the image.

```
 stage('Misconfig Scanner') {
            steps {
                sh """
                cd app/
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.json
                trivy config . -f json -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.json
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --exit-code 0 --severity HIGH,CRITICAL
                """
            }
        }

stage('Filesystem Scanner') {
            steps {
                sh """
                cd app/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.html
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.json
                trivy fs . -f json -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.json
                trivy fs . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportfs.html --exit-code 0 --severity HIGH,CRITICAL
                """
            }
        }

stage('Vulnerability and Secret Scanner') {
            steps {
                sh """
                cd app/deploy
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.json
                trivy image -f json -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.json ${params.nexusServer}/devsecops:v${BUILD_NUMBER}
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --exit-code 0 --severity HIGH,CRITICAL ${params.nexusServer}/devsecops:v${BUILD_NUMBER}
                """
            }
        }
```

the results of the checks carried out by trivy, will be directly exported into html files and will be directly moved to the web server directory. This will make it easier for us to see the results of the reports obtained.

We can use the command below, this is done if we want to thwart the pipeline process so that it does not proceed to the next step.

```
--exit-code 1
```

# ZAP Pipeline
ZAP or Zed Attack Proxy is an open-source and free penetration testing tool. In this section, we'll do a ZAP implementation in Jenkins. By using the image ```ictu/zap2docker-weekly```  which is created for automatic authentication scans, this pipeline will be done using a baseline scan. Baseline Scan will scan for about 1 minute and will perform passive scanning. ZAP will automatically fill in the login form so that ZAP can perform the scanning. 

The following is the pipeline used in this project:

```
stage('Zap Proxy') {
            steps {
                    sh """
                    docker run --rm -v /zap:/zap/wrk:rw -v /opt/hosts_zap:/etc/hosts -t ictu/zap2docker-weekly zap-baseline.py -I -j \
                    -t http://${params.appEndpoint} \
                    -r reportzap.html \
                    -J reportzap.json \
                    -g reportzap.conf \
                    --hook=/zap/auth_hook.py \
                    -z "auth.loginurl=http://${params.appEndpoint}/login \
                        auth.username="user" \
                        auth.password="password""
                    mkdir /var/www/html/zapReport/pipeline${BUILD_NUMBER}/
                    cp /zap/* /var/www/html/zapReport/pipeline${BUILD_NUMBER}/
                    rm -rf /zap/*
                    """
            }
        }
```

After the scanning process is complete, the results of the scanning will be exported in html and json form, and the config will also be exported. Then, Jenkins will create a new directory and copy the export file to the new directory. After that, jenkins will delete the original file.

Note: Every time we run Jenkins, Jenkins will create a new directory based on the pipeline. So we don't have to worry about losing the previous report.
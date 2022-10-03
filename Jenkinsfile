pipeline {
    agent any
    stages {
      stage("SCM Checkout"){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CleanBeforeCheckout', deleteUntrackedNestedRepositories: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github-panca', url: 'https://github.com/pancaperkasa/jenkins-devsecops.git']]])
            }
        }        
        stage('Sonnar-Scanner') {
            steps {
                    sh '''
                    cd app/
                    /home/jenkins/sonar-scanner/sonar-scanner/bin/sonar-scanner \
                    -Dsonar.projectKey="jenkins-devsecops" \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://10.8.60.126:9000/ \
                    -Dsonar.login="e1f735b437dd77262312033dfa83766cdbc3508c"
                    -Dsonar.qualitygate.wait=true
                    '''
            }
        }
        stage("Misconfig Scanner"){
            steps{
                sh '''
                cd app/
                mkdir /var/www/html/trivy/pipeline${BUILD_NUMBER}/
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html
                trivy config . --format template --template "@deploy/html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportconfig.html --exit-code 1 --severity HIGH,CRITICAL
                '''
            } 
        }
        stage('Create Image') {
            steps {
                sh '''
                docker image rm 10.8.60.126:5000/devsecops:v${BUILD_NUMBER} || echo "No existing image found"
                docker-compose -f app/deploy/docker-compose.yaml build --no-cache
                '''
            }
        }
        stage("Vulnerability and Secret Scanner"){
            steps{
                sh '''
                cd app/deploy
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretpython.html --exit-code 1  --ignore-unfixed --severity HIGH,CRITICAL 10.8.60.126:5000/devsecops:v${BUILD_NUMBER} 
                
                touch /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretdb.html
                trivy image --format template --template "@html.tpl" -o /var/www/html/trivy/pipeline${BUILD_NUMBER}/reportimagesecretdb.html --exit-code 1 --ignore-unfixed --severity HIGH,CRITICAL mariadb:10.6.3
                '''
            }
        }
        stage('Push Image to nexus') {
            steps {
                sh '''
                set +x
                docker login --username=admin --password=$nexusPassword 10.8.60.126:5000
                set -x
                docker push 10.8.60.126:5000/devsecops:v${BUILD_NUMBER}
                docker rmi 10.8.60.126:5000/devsecops:v${BUILD_NUMBER}
                docker images
                '''
            }
        }
        stage('Run the docker compose') {
            steps {
                sh '''
                docker-compose -f app/deploy/docker-compose.yaml stop
                docker-compose -f app/deploy/docker-compose.yaml up -d
                '''
            }
        }
    }
}
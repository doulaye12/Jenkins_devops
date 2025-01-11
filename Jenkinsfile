pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        DOCKER_USER = credentials('docker_user')
        DOCKER_TOKEN = credentials('docker_token')
        
    }

    stages {
        stage('Checkout') {
            steps {
                echo "---------------------- Checkout Main ------------------------------------------------"
                // Get some code from a GitHub repository
                echo "checked out ${env.BRANCH_NAME}"
            }
            
        }
        
        stage('Code Quality Check') {
            steps {

                echo "---------------------- Code QualityMain ------------------------------------------------"
                echo "We will run sonarQube here."

                withSonarQubeEnv('sonarqube') {
                    sh "mvn clean compile sonar:sonar -Dsonar.qualitygate.wait=true -Dsonar.projectKey=transaction-develop -Dsonar.projectName='transaction-develop'"
                }
            }
        }

        stage('Build') {
            steps {
               
                // Run Maven on a Unix agent.
                sh "mvn clean package"
               
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
                }
            }
        }

        stage('Dockerize develop') {

            when {
               branch 'develop'
            }

            steps {
               script {
                def dockerImage = "${DOCKER_USER}/transactions:${env.BRANCH_NAME}"
                echo "----------------- Build docker image : ${dockerImage}-----------------"
                sh """
                    docker build -f Dockerfile -t ${dockerImage} .
                    echo ${DOCKER_TOKEN} | docker login --username ${DOCKER_USER} --password-stdin
                    docker push ${dockerImage}

                    docker compose down
                    docker compose up -d
                """
               }
            }

            
        }

        stage('Dockerize release') {

            when {
               branch 'release/*'
            }

            steps {
               script {
                def dockerImage = "${DOCKER_USER}/transactions:release-${BUILD_NUMBER}"
                echo "----------------- Build docker image : ${dockerImage}-----------------"
                sh """
                    docker build -f Dockerfile -t ${dockerImage} .
                    echo ${DOCKER_TOKEN} | docker login --username ${DOCKER_USER} --password-stdin
                    docker push ${dockerImage}
                """
               }
            }

            
        }

        stage('Dockerize production') {

            when {
                allOf {
                    tag 'v*'
                }
            }

            steps {
               script {
                def dockerImage = "${DOCKER_USER}/transactions:${env.TAG_NAME}"
                echo "----------------- Build docker image : ${dockerImage}-----------------"
                sh "docker build -f Dockerfile -t ${dockerImage} ." 
                
                //sh "echo ${DOCKER_TOKEN} | docker login --username ${DOCKER_USER} --password-stdin"
                //sh "docker push ${dockerImage}"
               }
            }

            
        }

        stage('Security check') {

            when {
                allOf {
                    tag 'v*'
                }
            }

            steps {
               script {
                def dockerImage = "${DOCKER_USER}/transactions:${env.TAG_NAME}"
                
                // configuration trivy 
                
                sh "mkdir reports"
                sh "curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/html.tpl > html.tpl"
                sh "trivy image --ignore-unfixed --severity CRITICAL,HIGH --format template --template '@html.tpl' -o ./reports/transactions-report.html ${dockerImage}" 
                publishHTML target : [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'transactions-scan.html',
                    reportName: 'Trivy Scan',
                    reportTitles: 'Trivy Scan'
                ]
                
                //sh "echo ${DOCKER_TOKEN} | docker login --username ${DOCKER_USER} --password-stdin"
                //sh "docker push ${dockerImage}"
               }
            }

            
        }

    }
} 

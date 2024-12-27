pipeline {
    agent any

    tools {
        maven 'M3'
    }

    environment {
        DOCKER_USER = credentials('docker_user')
        DOCKER_TOKEN = credentials('docker_token')
        SONAR_HOST = 'http://localhost:9000'
        SONARQUBETOKEN = 'sqp_2128838ea70bab7f2e51545b68684f0f97610e21'
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
                sh "mvn clean verify sonar:sonar -Dsonar.token=$SONARQUBETOKEN -Dsonar.projectKey=transactions-api -Dsonar.projectName='transactions-api' -Dsonar.host.url=${env.SONAR_HOST}"
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
                    archiveArtifacts artifacts: '**/*.jar', followSymlinks: false
                }
            }
        }

        stage('Dockerize') {
            steps {
               script {
                def dockerImage = "${DOCKER_USER}/transactions"
                echo "----------------- Build docker image : ${dockerImage}-----------------"
                sh "docker build -f Dockerfile -t ${dockerImage} ." 
               }
            }

            
        }

        stage('Docker Publish') {
            steps {
                script {
                    def dockerImage = "${DOCKER_USER}/transactions"
                    sh """
                    echo ${DOCKER_TOKEN} | docker login --username ${DOCKER_USER} --password-stdin
                    docker push ${dockerImage}
                    """
               }
            
                
            }

        }


        stage('Docker Compose') {
            steps {
                sh """
                docker compose down
                docker compose up -d
                """
            }
        }
    }
} 

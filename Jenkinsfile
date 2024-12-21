pipeline {
    agent any

    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                echo "---------------------- Checkout Main ------------------------------------------------"
                // Get some code from a GitHub repository
                echo "checked out ${env.BRANCH_NAME}"
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
               
                // Run Maven on a Unix agent.
                //sh "mvn -Dmaven.test.failure.ignore=true clean package"
                echo "Should be done"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            /*post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    
                }
            }*/
        }

        stage('Docker Publish') {
            steps {
               
                // Run Maven on a Unix agent.
                //sh "mvn -Dmaven.test.failure.ignore=true clean package"
                echo "Should be done"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            /*post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    
                }
            }*/
        }


        stage('Docker Compose') {
            steps {
               
                // Run Maven on a Unix agent.
                //sh "mvn -Dmaven.test.failure.ignore=true clean package"
                echo "Should be done"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            /*post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    
                }
            }*/
        }
    }
} 
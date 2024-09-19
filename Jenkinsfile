pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run Maven build and skip tests
                    sh 'mvn clean package -DskipTests=true'
                }
            }

            post {
                success {
                    // Archive the jar files
                    archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Run unit tests with Maven and generate Jacoco code coverage report
                    sh 'mvn clean test jacoco:report'
                }
            }

            post {
                success {
                    // Publish Jacoco code coverage report
                    jacoco execPattern: '**/target/jacoco.exec', 
                           classPattern: '**/target/classes', 
                           sourcePattern: '**/src/main/java', 
                           inclusionPattern: '**/*.class'
                }
                always {
                    // Archive the test results (JUnit format)
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
    }

    post {
        always {
            // Always clean up workspace after build
            cleanWs()
        }
    }
}

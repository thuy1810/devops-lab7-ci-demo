pipeline {
    agent any

    options {
        skipDefaultCheckout()
    }

    tools {
        maven 'M3'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                dir('sample-java-app') {
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Test') {
            steps {
                dir('sample-java-app') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    junit 'sample-java-app/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                dir('sample-java-app') {
                    sh 'mvn package -DskipTests'
                }
                archiveArtifacts artifacts: 'sample-java-app/target/*.jar', fingerprint: true
            }
        }
    }
}
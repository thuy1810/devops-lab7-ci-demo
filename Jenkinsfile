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
                stage('Publish to Nexus') {
            steps {
                dir('sample-java-app') {
                    withCredentials([usernamePassword(
                        credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USERNAME',
                        passwordVariable: 'NEXUS_PASSWORD'
                    )]) {
                        configFileProvider([configFile(
                            fileId: 'maven-settings-nexus',
                            variable: 'MAVEN_SETTINGS'
                        )]) {
                            sh '''
                                mvn -B -s "$MAVEN_SETTINGS" deploy:deploy-file \
                                    -DpomFile=pom.xml \
                                    -Dfile=target/ci-demo-1.0.0-SNAPSHOT.jar \
                                    -Durl=http://lab7-nexus:8081/repository/maven-snapshots/ \
                                    -DrepositoryId=nexus
                            '''
                        }
                    }
                }
            }
        }
    }
}
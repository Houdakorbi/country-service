pipeline {
    agent any
    tools {
        jdk 'JDK21'
        maven 'M2_HOME'
    }
    stages {
        stage('Checkout code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Houdakorbi/country-service.git',
                    credentialsId: '8aa911b1-3f65-41ec-a401-880b729d3c56'
            }
        }
        stage('Compile, test, package and install') {
            steps {
                sh 'mvn clean install'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'MySonarQubeServer', credentialsId: 'sonarqubePWD') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=country-service -Dsonar.projectName=country-service'
                }
            }
        }
        stage('Deploy jar to Nexus') {
            steps {
                sh 'mvn deploy -DskipTests'
            }
        }
        stage('Upload reports to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexusCred',
                                 usernameVariable: 'NUSER', passwordVariable: 'NPASS')]) {
                    sh '''
                        cd target
                        rm -f jacoco-report.zip surefire-reports.zip
                        zip -r jacoco-report.zip site/jacoco
                        zip -r surefire-reports.zip surefire-reports
                        curl -f -u "$NUSER:$NPASS" --upload-file jacoco-report.zip \
                          http://localhost:8081/repository/reports/country-service/build-$BUILD_NUMBER/jacoco-report.zip
                        curl -f -u "$NUSER:$NPASS" --upload-file surefire-reports.zip \
                          http://localhost:8081/repository/reports/country-service/build-$BUILD_NUMBER/surefire-reports.zip
                    '''
                }
            }
        }
    }
}

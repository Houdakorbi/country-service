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
                    sh 'mvn sonar:sonar -Dsonar.projectKey=country-service -Dsonar.projectName=country-service'
                }
            }
        }
    }
}

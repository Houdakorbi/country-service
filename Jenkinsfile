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
        stage('Compile code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test code') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Packages code') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }
    }
}

pipeline {
    agent any  

    tools {
        
        sonarqubeScanner 'sonarqube-scanner'
    }

    stages {
        stage('Récupération du code') {
            steps {
                git url: 'https://github.com/manel016/devops.git', branch: 'Fourat'
            }
        }

        stage('Afficher la date') {
            steps {
                script {
                    def now = new Date()
                    echo "Date et heure actuelles : ${now}"
                }
            }
        }

        stage('MVN CLEAN') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('MVN COMPILE') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Tests et analyse SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }
}
pipeline {
    agent any  // Utilise un agent Jenkins disponible

    environment {
        // Variables d'environnement si besoin
        PROJECT_NAME = 'demo-project'
        SONAR_TOKEN = credentials('sonar-token') // 🔒 Jeton SonarQube stocké dans Jenkins
    }

    stages {

        stage('git') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/manel']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'githubtoken',
                        url: 'https://github.com/manel016/devops.git'
                    ]]
                )
            }
        }

        stage('Compiler le projet') {
            steps {
                echo 'Compilation avec Maven...'
                dir('Order') {
                     sh 'mvn clean package'
                }
            }
        }

        stage('Tests unitaires') {
            steps {
                echo 'Lancement des tests...'
                dir('Order') {
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Analyse SonarQube...'
                
                    withSonarQubeEnv('sonarqube') {
                         dir('Order') {
                        sh """
                            mvn clean verify sonar:sonar \
                            -Dsonar.projectKey=sonarqube \
                            -Dsonar.projectName="sonarqube" \
                            -Dsonar.token=${SONAR_TOKEN}
                        """
                         }
                    
                }
            }
        }

        stage('Analyse statique (optionnel)') {
            steps {
                echo 'Analyse statique (ex: Checkstyle, PMD, SonarQube)'
                // Exemple : sh 'mvn checkstyle:check'
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Le pipeline a échoué'
        }
        always {
            echo 'Fin du pipeline (success ou échec)'
        }
    }
}

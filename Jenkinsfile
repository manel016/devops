pipeline {
    agent any    

    environment {
        IMAGE_NAME = '95494016manel/magdoulimanel'
        IMAGE_TAG = 'latest'
        PROJECT_NAME = 'demo-project'
        SONAR_HOST_URL = 'http://192.168.33.10:9000'
        SONAR_TOKEN = credentials('sonarqube') // Jeton SonarQube dans Jenkins (Manage Credentials)
    }

    stages {

        stage('Git Checkout') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/manel']],
                    userRemoteConfigs: [[
                        credentialsId: 'githubtoken',
                        url: 'https://github.com/manel016/devops.git'
                    ]]
                ])
            }
        }

        stage('Compilation du projet') {
            steps {
                echo '⚙️ Compilation avec Maven...'
                dir('Order') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Tests unitaires') {
            steps {
                echo '🧪 Exécution des tests unitaires...'
                dir('Order') {
                    sh 'mvn test'
                }
            }
        }

        stage('Analyse SonarQube') {
            steps {
                echo '🔍 Analyse SonarQube en cours...'
                dir('Order') {
                    withSonarQubeEnv('sonarqube') {
                        sh """
                            mvn sonar:sonar \
                                -Dsonar.projectKey=${PROJECT_NAME} \
                                -Dsonar.host.url=${SONAR_HOST_URL} \
                                -Dsonar.login=${SONAR_TOKEN}
                        """
                    }
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                echo '🐳 Construction et push de l’image Docker...'
                dir('Order') {
                    script {
                            def image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                           
                        }
                    }
                }
            }
        

        stage('Lister les images Docker') {
            steps {
                echo '📋 Liste des images Docker disponibles sur Jenkins :'
                sh 'docker images'
            }
        }

        stage('Analyse statique (optionnelle)') {
            steps {
                echo '🧩 Analyse statique (Checkstyle, PMD, etc.)'
                // Exemple : sh 'mvn checkstyle:check'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminé avec succès ! '
        }
        failure {
            echo '❌ Le pipeline a échoué.'
        }
        always {
            echo '📦 Fin du pipeline (succès ou échec).'
        }
    }
}

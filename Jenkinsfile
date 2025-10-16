pipeline {
    agent any  // Utilise un agent Jenkins disponible

    environment {
        // Variables d'environnement si besoin
         IMAGE_NAME = '95494016manel/magdoulimanel'
        PROJECT_NAME = 'demo-project'
        SONAR_HOST_URL = 'http://192.168.33.10:9000'
        SONAR_TOKEN = credentials('sonarqube') // 🔒 Jeton SonarQube stocké dans Jenkins
    }
 
    stages {

        stage('git') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/manel']],
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

        stage('Tests unitaires' ) {
            steps {
                echo 'Lancement des tests...'
                dir('Order') {
                    sh 'mvn test'
                }
            }
        }
   stage('SonarQube Analysis') {
    steps {
        echo 'Analyse SonarQube en cours...'
        dir('Order') {
            withSonarQubeEnv('sonarqube') { // Nom du serveur configuré dans Jenkins → Manage Jenkins → SonarQube servers
                sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=sample_project \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.login=${SONAR_TOKEN}
                """
            }
        }
    }
}
       
        stage('Build Docker Imagee') {
    steps {
        dir('Order') {
            script {
                docker.withRegistry('', 'dockerhub-credentials') {
                    def image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                    image.push()
                }
            }
        }
    }
}

        stage('List Docker Images') {
            steps {
                sh 'docker images'
            }
        }
      

        stage('Annalyse statique') {
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

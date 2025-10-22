pipeline {
    agent any

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
    }
}





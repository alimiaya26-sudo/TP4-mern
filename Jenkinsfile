pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Nettoyage') {
            steps {
                sh 'docker compose down || true'
                sh 'docker rm -f mongodb server client || true'
            }
        }

        stage('Build des images') {
            steps {
                sh 'docker build -t mern-server ./Server'
                sh 'docker build -t mern-client ./Client'
            }
        }

        stage('Lancement') {
            steps {
                sh 'docker compose up -d --build'
            }
        }

        stage('Verification') {
            steps {
                sh 'docker compose ps'
            }
        }

        stage('Test serveur') {
            steps {
                sh '''
                  for i in 1 2 3 4 5 6 7 8 9 10; do
                    docker exec server wget -qO- http://localhost:9000/api/health && exit 0
                    sleep 3
                  done
                  exit 1
                '''
            }
        }

        stage('Test client') {
            steps {
                sh 'docker exec client wget -qO- http://localhost:3000 | grep root'
            }
        }
    }

    post {
        success {
            echo 'Pipeline reussi : application disponible sur http://localhost:3000'
        }
        failure {
            sh 'docker compose logs'
            sh 'docker compose down || true'
        }
    }
}

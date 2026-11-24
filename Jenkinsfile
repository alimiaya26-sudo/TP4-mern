def run(String cmd) {
    if (isUnix()) {
        sh cmd
    } else {
        bat cmd
    }
}

pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Info Docker') {
            steps {
                script {
                    run('docker version')
                    run('docker compose version')
                }
            }
        }

        stage('Build des images') {
            steps {
                script {
                    run('docker build -t mern-server ./Server')
                    run('docker build -t mern-client ./Client')
                }
            }
        }

        stage('Lancement') {
            steps {
                script {
                    run('docker compose up -d --build')
                }
            }
        }

        stage('Verification') {
            steps {
                script {
                    run('docker compose ps')
                }
            }
        }

        stage('Test serveur') {
            steps {
                retry(10) {
                    sleep 3
                    script {
                        run('docker exec server wget -qO- http://localhost:9000/api/health')
                    }
                }
            }
        }

        stage('Test client') {
            steps {
                script {
                    run('docker exec client wget -qO- http://localhost:3000')
                }
            }
        }
    }
}

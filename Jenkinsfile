pipeline {
    agent any

    environment {
        IMAGE_NAME = 'citrafadillahagung/simple-app'
        REGISTRY = 'https://index.docker.io/v1/'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
        DOCKER_EXE = 'C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Melakukan checkout dari SCM...'
                checkout scm
            }
        }

        stage('Check Docker') {
            steps {
                bat '"%DOCKER_EXE%" --version'
                bat '"%DOCKER_EXE%" info'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '"%DOCKER_EXE%" build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
                echo 'Image berhasil dibuat.'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '"%DOCKER_EXE%" login -u "%DOCKER_USER%" -p "%DOCKER_PASS%"'
                    bat '"%DOCKER_EXE%" push %IMAGE_NAME%:%BUILD_NUMBER%'
                    bat '"%DOCKER_EXE%" tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest'
                    bat '"%DOCKER_EXE%" push %IMAGE_NAME%:latest'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline selesai'
        }
    }
}

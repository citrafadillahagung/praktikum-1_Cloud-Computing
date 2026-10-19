
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'citrafadillahagung/simple-app'
        REGISTRY = 'https://index.docker.io/v1/'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
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
                script {
                    if (isUnix()) {
                        sh 'docker --version'
                        sh 'docker info'
                    } else {
                        bat 'docker --version'
                        bat 'docker info'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
                    } else {
                        bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
                    }
                }
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
                    script {
                        if (isUnix()) {
                            sh '''
                                set +x
                                echo "$DOCKER_PASS" | docker login \
                                    -u "$DOCKER_USER" --password-stdin
                                docker push "${IMAGE_NAME}:${BUILD_NUMBER}"
                                docker tag "${IMAGE_NAME}:${BUILD_NUMBER}" \
                                    "${IMAGE_NAME}:latest"
                                docker push "${IMAGE_NAME}:latest"
                            '''
                        } else {
                            bat '''
                                @echo off
                                docker login -u "%DOCKER_USER%" --password-stdin
                                docker push "%IMAGE_NAME%:%BUILD_NUMBER%"
                                docker tag "%IMAGE_NAME%:%BUILD_NUMBER%" "%IMAGE_NAME%:latest"
                                docker push "%IMAGE_NAME%:latest"
                            '''
                        }
                    }
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

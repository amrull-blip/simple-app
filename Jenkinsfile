pipeline {
    agent any

    environment {
        IMAGE_NAME = 'ruulls/simple-app'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }


        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
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
                    bat '''
                        @echo off
                        powershell -NoProfile -NonInteractive -Command "$env:DOCKER_PASS | docker login -u $env:DOCKER_USER --password-stdin"
                        if errorlevel 1 exit /b 1

                        docker push %IMAGE_NAME%:%BUILD_NUMBER%
                        if errorlevel 1 exit /b 1

                        docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest
                        if errorlevel 1 exit /b 1

                        docker push %IMAGE_NAME%:latest
                        if errorlevel 1 exit /b 1

                        docker logout
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Selesai menjalankan pipeline'
        }
    }
}

pipeline {
    agent any

    environment {
        IMAGE_NAME = 'nikhilabba12/realwell:latest'
        CONTAINER_NAME = 'realwell-container'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-creds',
                    url: 'https://github.com/MADHU8912/realwell.git'
            }
        }

        stage('Check Files') {
            steps {
                bat 'dir'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%'
            }
        }

        stage('Pull Docker Image') {
            steps {
                bat 'docker pull %IMAGE_NAME%'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker rm -f %CONTAINER_NAME% || exit /b 0'
                bat 'docker run -d --name %CONTAINER_NAME% %IMAGE_NAME%'
            }
        }
    }

    post {
        always {
            bat 'docker logout || exit /b 0'
        }
        success {
            echo 'Realwell pipeline completed successfully'
        }
        failure {
            echo 'Realwell pipeline failed'
        }
    }
}
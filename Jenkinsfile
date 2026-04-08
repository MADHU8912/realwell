pipeline {
    agent any

    environment {
        IMAGE_NAME = 'nikhilabba12/realwell'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Check Files') {
            steps {
                bat 'dir'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker logout || exit /b 0'
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }

        stage('Pull Docker Image') {
            steps {
                bat 'docker pull %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker rm -f realwell-container || exit /b 0'
                bat 'docker run -d --name realwell-container %IMAGE_NAME%:%IMAGE_TAG%'
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

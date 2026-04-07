pipeline {
    agent any

    environment {
        IMAGE_NAME = 'nikhilabba12/realwell'
        IMAGE_TAG = 'latest'
        CONTAINER_NAME = 'realwell-container'
    }

    stages {
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
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    powershell '''
                        docker logout | Out-Null
                        $env:DOCKER_PASS | docker login -u $env:DOCKER_USER --password-stdin
                    '''
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
                bat 'docker rm -f %CONTAINER_NAME% || exit /b 0'
                bat 'docker run -d --name %CONTAINER_NAME% %IMAGE_NAME%:%IMAGE_TAG%'
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
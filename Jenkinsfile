pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'
        IMAGE_NAME = "AresEssentinalandEror/phpinfo-test"
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy locally for test') {
            steps {
                sh '''
                    docker stop phpinfo-test || true
                    docker rm phpinfo-test || true
                    docker run -d --name phpinfo-test -p 8888:80 ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f || true'
        }
        success {
            echo "Готово! Проверь приложение: http://localhost:8888 или http://<IP_VM>:8888"
        }
    }
}

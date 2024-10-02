pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "my-redis"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").run("-d -p 6379:6379 --name redis-server")
                }
            }
        }
    }

    post {
        failure {
            echo 'The Pipeline failed :('
        }
    }
}
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "redis"
        DOCKER_TAG = "latest"
    }

    stages {
        stage('Remove Old Container') {
            steps {
                script {
                    // Si el contenedor redis-server existe, lo eliminamos
                    sh 'docker rm -f redis-server || true'
                }
            }
        }
        stage('Run New Redis Container') {
            steps {
                script {
                    // Desplegamos un nuevo contenedor Redis
                    docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").run("-d -p 6379:6379 --name redis-server")
                }
            }
        }
    }

    post {
        failure {
            echo 'The Pipeline failed :('
        }
        success {
            echo 'Redis container started successfully!'
        }
    }
}

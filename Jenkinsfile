pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git 'https://github.com/tu-repositorio/redis-deployment.git'
            }
        }

        stage('Build Redis Docker Image') {
            steps {
                script {
                    docker.build('mi-redis:latest', '-f Dockerfile.redis .')
                }
            }
        }

        stage('Deploy Redis') {
            steps {
                script {
                    docker.image('mi-redis:latest').run('--name redis-server -d -p 6379:6379')
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
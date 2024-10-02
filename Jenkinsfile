pipeline {
    agent any

        stage('Clone repository') {
            steps {
                git credentialsId: 'git-redis', url: 'https://github.com/BenJameto/redis'
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
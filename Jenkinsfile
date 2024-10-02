pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git credentialsId: 'git-redis', url: 'https://github.com/BenJameto/redis'
            }
        }

        stage('Deploy Redis') {
            steps {
                script {
                    docker.image('redis:latest').run('--name redis-server -d -p 6379:6379')
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

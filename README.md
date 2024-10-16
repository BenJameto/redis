Este es un ejercicio para comprender el flujo de integracion y despliegue continuo usando la herramienta de Jenkins, Docker, Minikube y cubernetes. 
para poder ejecutar este ejercicio es necesario tener instalado Jenkins de manera local o en contenedor, asi mismo tener configurado docker y kubernetes, para evitar problemas al momento del despliegue.


pipeline:

pipeline {
    agent any
    
    environment {
        KUBECONFIG = "/home/jenkins/.kube/config"
    }
    
    stages {
        stage('Clonar Repositorio') {
            steps {
                // Clonar el repositorio de GitHub
                git branch: 'main', url: 'https://github.com/tu-usuario/tu-repo.git'
            }
        }

        stage('Construir Imagen Docker (opcional)') {
            when {
                expression { fileExists('Dockerfile') } // Sólo si hay un Dockerfile
            }
            steps {
                script {
                    docker.build('tu-usuario/redis-custom:latest')
                }
            }
        }

        stage('Autenticar en DockerHub (opcional)') {
            when {
                expression { fileExists('Dockerfile') } // Sólo si construyes una imagen
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Subir Imagen a DockerHub (opcional)') {
            when {
                expression { fileExists('Dockerfile') } // Sólo si construyes una imagen
            }
            steps {
                sh 'docker push tu-usuario/redis-custom:latest'
            }
        }

        stage('Desplegar Redis en Minikube') {
            steps {
                // Aplicar el manifiesto de Kubernetes
                sh 'kubectl apply -f redis-deployment.yaml'
            }
        }
        
        stage('Verificar Despliegue') {
            steps {
                // Verificar que el pod y el servicio de Redis están corriendo
                sh 'kubectl get pods'
                sh 'kubectl get services'
            }
        }
    }

    post {
        success {
            echo 'Redis desplegado exitosamente en Minikube!'
        }
        failure {
            echo 'El despliegue falló. Revisa los logs para más detalles.'
        }
    }
}


pipeline {
    agent any

    stages {
        stage('Clonar el repositorio') {
            steps {
                // Clonar el repositorio desde GitHub para obtener el archivo nginx-deployment.yaml
                git url: 'https://github.com/BenJameto/redis.git', branch: 'main'
            }
        }

        stage('Desplegar en Kubernetes') {
            steps {
                script {
                    // Aplicar el archivo nginx-deployment.yaml en Kubernetes
                    sh 'kubectl apply -f redis-deployment.yaml'
                }
            }
        }

        stage('Verificar Despliegue') {
            steps {
                // Verificar que el pod y el servicio de Redis están corriendo
                sh 'kubectl get pods --all-namespaces'
                sh 'kubectl get services -n jenkins'
            }
        }
    }

    post {
        success {
            echo 'Despliegue completado exitosamente.'
        }
        failure {
            echo 'El despliegue ha fallado.'
        }
    }
}

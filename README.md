
# Despliegue Automático de Redis Usando CI/CD con Jenkins y GitHub

Este documento proporciona una guía paso a paso para configurar un pipeline de CI/CD para desplegar Redis utilizando Jenkins, GitHub y Kubernetes. El pipeline está configurado para desplegar Redis automáticamente cada vez que se realiza un commit en la rama `main` en GitHub.

## 1. Configuración en GitHub

1. **Crear un Repositorio en GitHub**: Para este caso solo basta clonar el repositorio.

2. **Configura un Webhook en GitHub**:
   - Ve a **Settings** > **Webhooks** en tu repositorio.
   - Haz clic en **Add webhook** y establece la **Payload URL** con el endpoint de Jenkins para el webhook (en mi caso, `http://192.168.49.2:30000/github-webhook/`).
   - Selecciona **Content type** como `application/json`.
   - Asegúrate de seleccionar **Just the push event** para que el webhook se active solo cuando haya cambios en `main`.

## 2. Configuración en Jenkins

1. **Instala los Plugins Necesarios**:
   - Ve a **Manage Jenkins** > **Manage Plugins**.
   - Asegúrate de que los siguientes plugins estén instalados: **Git Plugin**, **Pipeline Plugin** y **GitHub Integration Plugin**.

2. **Agrega GitHub en Jenkins**:
   - Ve a **Manage Jenkins** > **Configure System** > **GitHub**.
   - Agrega tus credenciales de GitHub y verifica la conexión.

3. **Crea un Nuevo Job de Pipeline**:
   - Ve a **New Item** en Jenkins y selecciona **Pipeline**.
   - Nombra el job, por ejemplo, `deploy-redis`.
   - En la sección **Pipeline**, selecciona **Pipeline script from SCM** y configura lo siguiente:
     - **SCM**: Selecciona **Git**.
     - **Repository URL**: Ingresa la URL de tu repositorio de GitHub.
     - **Branch Specifier**: Ingresa `*/main` para rastrear solo la rama `main`.
     - **Script Path**: Asegúrate de que apunte al archivo `Jenkinsfile` en el repositorio.

## 3. Jenkinsfile para Desplegar Redis

En tu repositorio de GitHub (rama main), crea o actualiza el archivo `Jenkinsfile` con el siguiente contenido:

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Deploy Redis') {
            steps {
                script {
                    sh 'kubectl apply -f redis-deployment.yaml'
                }
            }
        }
    }
    post {
        success {
            echo 'Redis ha sido desplegado exitosamente.'
        }
        failure {
            echo 'Hubo un error en el despliegue de Redis.'
        }
    }
}
```

## 4. Verificación

1. **Prueba el Webhook**:
   - Realiza un commit en la rama `main` en GitHub (por ejemplo, actualiza el archivo README) para activar el pipeline en Jenkins.

2. **Monitorea el Despliegue**:
   - Ve al Dashboard de Jenkins y observa el job del pipeline para asegurarte de que se complete correctamente y que Redis se despliegue en el clúster de Kubernetes.

## Conclusión

Siguiendo estos pasos, tendrás un pipeline de CI/CD funcional que despliega Redis automáticamente con cada commit en la rama `main`. Esta configuración permite un despliegue continuo usando GitHub y Jenkins, mejorando la automatización y eficiencia del proceso de despliegue.

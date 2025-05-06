pipeline {
  agent any

  // Variables de entorno para parametrizar rutas
  environment {
    // Ruta de despliegue en la máquina local
    DEPLOY_DIR = 'C:\\Deployments\\Residencial'
  }

  stages {
    stage('Checkout') {
      steps {
        // Clonar el repositorio completo
        checkout scm
      }
    }

    stage('Publish HTML') {
      steps {
        // 1) Crear la carpeta 'output' si no existe
        bat 'if not exist output mkdir output'

        // 2) Copiar index.html a output
        bat '''
          if exist index.html (
            copy /Y index.html output\\
          ) else (
            echo ERROR: index.html no encontrado en la raíz del workspace
            exit /b 1
          )
        '''
      }
    }

    stage('Deploy') {
      steps {
        // 1) Asegurarse de que exista el directorio de despliegue
        bat "if not exist \"${DEPLOY_DIR}\" mkdir \"${DEPLOY_DIR}\""

        // 2) Copiar todo el contenido de output a DEPLOY_DIR
        bat "xcopy /E /I /Y output\\\\* \"${DEPLOY_DIR}\\\\\""
      }
    }
  }

  post {
    always {
      // Publicar el reporte HTML en Jenkins UI
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML'
      ]
    }
  }
}

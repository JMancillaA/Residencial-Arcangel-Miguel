pipeline {
  agent any

  // Dispara una comprobación cada minuto
  triggers {
    pollSCM('*/1 * * * *')
  }

  // Variables de entorno
  environment {
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
        // 1) Si existe 'output', eliminarla; luego crearla limpia
        bat '''
          if exist output (
            rmdir /S /Q output
          )
          mkdir output
        '''
        // 2) Copiar recursivamente TODO el contenido del workspace a 'output'
        bat 'xcopy /E /I /Y .\\* output\\'
      }
    }

    stage('Deploy') {
      steps {
        // 1) Asegurarse de que exista el directorio de despliegue
        bat "if not exist \"${DEPLOY_DIR}\" mkdir \"${DEPLOY_DIR}\""
        // 2) Copiar recursivamente todo el contenido de 'output' a DEPLOY_DIR
        bat "xcopy /E /I /Y output\\\\* \"${DEPLOY_DIR}\\\\\""
      }
    }
  }

  post {
    always {
      // Publicar en la UI de Jenkins el sitio HTML generado
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML'
      ]
    }
  }
}

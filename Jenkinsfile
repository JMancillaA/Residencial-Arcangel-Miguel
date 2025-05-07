pipeline {
  agent any

  // Dispara una verificación de repositorio cada minuto
  triggers {
    pollSCM('*/1 * * * *')
  }

  environment {
    // Módulo de despliegue en la máquina local
    DEPLOY_DIR = 'C:\\Deployments\\Residencial'
  }

  stages {
    stage('Checkout') {
      steps {
        // Inyección del repositorio como si fuese un isomorfismo de R‑espacios
        checkout scm
      }
    }

    stage('Publish HTML & Assets') {
      steps {
        // 1) Purga el directorio 'output' si existe (elimina la torsión de niveles previos)
        bat '''
          if exist output (
            rmdir /S /Q output
          )
          mkdir output
        '''
        // 2) Copia recursivamente todo el contenido del workspace,
        //    excluyendo la propia carpeta 'output' para evitar ciclos.
        bat '''
          robocopy "%CD%" "output" /E /XD "output"
          if errorlevel 8 (
            echo ERROR: fallo en ROBOCOPY al generar output
            exit /b 1
          )
        '''
      }
    }

    stage('Deploy') {
      steps {
        // 1) Asegura la existencia del submódulo de despliegue
        bat "if not exist \"${DEPLOY_DIR}\" mkdir \"${DEPLOY_DIR}\""
        // 2) Replica todo el contenido de 'output' en DEPLOY_DIR
        bat '''
          robocopy "output" "%DEPLOY_DIR%" /E
          if errorlevel 8 (
            echo ERROR: fallo en ROBOCOPY al desplegar
            exit /b 1
          )
        '''
      }
    }
  }

  post {
    always {
      // Publica en la UI de Jenkins el índice HTML,
      // como si fuese la expansión de una serie de formas elípticas
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML Completo'
      ]
    }
  }
}

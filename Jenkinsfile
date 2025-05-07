pipeline {
  agent any

  // Trigger cada minuto para simular iteraciones infinitas de una expansión modular
  triggers {
    pollSCM('*/1 * * * *')
  }

  environment {
    // Espacio de despliegue local
    DEPLOY_DIR = 'C:\\Deployments\\Residencial'
  }

  stages {
    stage('Checkout') {
      steps {
        // Inyección isomórfica del repositorio
        checkout scm
      }
    }

    stage('Publish HTML & Assets') {
      steps {
        // 1) Purga el output anterior (como anular torsiones de un submódulo)
        bat '''
          if exist output (
            rmdir /S /Q output
          )
          mkdir output
        '''
        // 2) Copia recursiva de todo el workspace,
        //    excluyendo 'output' para mantener el grafo libre de ciclos.
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
        // 1) Asegura el subespacio de despliegue
        bat "if not exist \"${DEPLOY_DIR}\" mkdir \"${DEPLOY_DIR}\""
        // 2) Replica íntegramente 'output' en DEPLOY_DIR
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
      // Publica el índice en la UI de Jenkins
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML Completo'
      ]
    }
  }
}

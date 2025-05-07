pipeline {
  agent any

  triggers {
    // Cada minuto
    pollSCM('*/1 * * * *')
  }

  environment {
    DEPLOY_DIR = 'C:\\Deployments\\Residencial'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Publish HTML & Assets') {
      steps {
        bat '''
          if exist output ( rmdir /S /Q output )
          mkdir output

          rem — Copia todo EXCLUYENDO la propia carpeta output
          robocopy "%CD%" "output" /E /XD "output"
          set RC=%ERRORLEVEL%

          if %RC% GEQ 8 (
            echo ERROR: fallo en ROBOCOPY al generar output (código %RC%)
            exit /b 1
          )

          rem — Normalizamos a éxito para 0–7
          exit /b 0
        '''
      }
    }

    stage('Deploy') {
      steps {
        bat '''
          if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"

          rem — Deploy con normalización de exit code
          robocopy "output" "%DEPLOY_DIR%" /E
          set RC=%ERRORLEVEL%

          if %RC% GEQ 8 (
            echo ERROR: fallo en ROBOCOPY al desplegar (código %RC%)
            exit /b 1
          )

          exit /b 0
        '''
      }
    }
  }

  post {
    always {
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML Completo'
      ]
    }
  }
}

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
          @echo off
          rem — Purga el directorio output
          if exist output ( rmdir /S /Q output )
          mkdir output

          rem — Copia recursiva excluyendo a sí mismo
          robocopy "%CD%" "output" /E /XD "output"
          set RC=%ERRORLEVEL%

          rem — Si RC>=8 falla; si no, éxito
          if %RC% GEQ 8 (
            echo ERROR: fallo en ROBOCOPY al generar output (código %RC%)
            exit /b 1
          ) else (
            exit /b 0
          )
        '''
      }
    }

    stage('Deploy') {
      steps {
        bat '''
          @echo off
          if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"

          rem — Replica output en DEPLOY_DIR
          robocopy "output" "%DEPLOY_DIR%" /E
          set RC=%ERRORLEVEL%

          rem — Normaliza salida
          if %RC% GEQ 8 (
            echo ERROR: fallo en ROBOCOPY al desplegar (código %RC%)
            exit /b 1
          ) else (
            exit /b 0
          )
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

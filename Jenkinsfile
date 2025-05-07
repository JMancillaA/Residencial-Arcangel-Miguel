pipeline {
  agent any

  triggers {
    // Cada minuto: mantiene el pipeline “recursivo” sin colapsar
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
        bat label: 'Publish HTML & Assets', script: """
          @echo off
          rem — Habilita expansión retrasada de variables
          setlocal enabledelayedexpansion

          rem — Purga el directorio output si existe
          if exist output (
            echo [Publish] Eliminando carpeta output anterior...
            rmdir /S /Q output
          )
          mkdir output

          rem — Copia recursiva de TODO el workspace, excluyendo output
          echo [Publish] Ejecutando ROBOCOPY desde %CD% hacia output...
          robocopy "%CD%" "output" /E /XD "output"
          set RC=!ERRORLEVEL!

          echo [Publish] ROBOCOPY exit code: !RC!
          rem — Sólo error si código ≥ 8
          if !RC! GEQ 8 (
            echo [Publish] ERROR: ROBOCOPY falló con código !RC!
            exit /b 1
          )

          endlocal
          echo [Publish] Completo exitosamente.
          exit /b 0
        """
      }
    }

    stage('Deploy') {
      steps {
        bat label: 'Deploy to Local', script: """
          @echo off
          setlocal enabledelayedexpansion

          rem — Asegura carpeta de despliegue
          if not exist "%DEPLOY_DIR%" (
            echo [Deploy] Creando carpeta de despliegue...
            mkdir "%DEPLOY_DIR%"
          )

          rem — Replica output en DEPLOY_DIR
          echo [Deploy] Ejecutando ROBOCOPY de output hacia %DEPLOY_DIR%...
          robocopy "output" "%DEPLOY_DIR%" /E
          set RC=!ERRORLEVEL!

          echo [Deploy] ROBOCOPY exit code: !RC!
          if !RC! GEQ 8 (
            echo [Deploy] ERROR: ROBOCOPY falló al desplegar (¡código !RC!!)
            exit /b 1
          )

          endlocal
          echo [Deploy] Despliegue completado.
          exit /b 0
        """
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

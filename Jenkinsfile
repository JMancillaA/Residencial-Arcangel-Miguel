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
          bat '''
          @echo off
          setlocal enabledelayedexpansion

          set "SRC=%WORKSPACE%\\output"
          set "DST=C:\\Deployments\\Residencial"

          rem Copia todo excepto la carpeta .git y el propio Jenkinsfile
          robocopy "!SRC!" "!DST!" /E /DCOPY:DA /COPY:DAT ^
                  /XD ".git" /XF Jenkinsfile
          set "RC=!ERRORLEVEL!"
          echo [Deploy] ROBOCOPY exit code: !RC!

          if !RC! GEQ 8 (
              echo [Deploy] **Fallo de Robocopy**
              endlocal & exit /b 1
          )

          echo [Deploy] Despliegue completado.
          endlocal & exit /b 0
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

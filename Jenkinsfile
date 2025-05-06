pipeline {
  agent any

  triggers {
    // Cada minuto comprueba si hay commits nuevos
    pollSCM('H/1 * * * *')
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Publish HTML') {
      steps {
        // 1) Asegurarse de que exista la carpeta output
        bat 'if not exist output mkdir output'

        // 2) Copiar el index.html de la raíz a output
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
  }

  post {
    always {
      publishHTML target: [
        reportDir:   'output',
        reportFiles: 'index.html',
        reportName:  'Sitio HTML'
      ]
    }
  }
}

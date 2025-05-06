pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Publish HTML') {
      steps {
        // Crear carpeta de salida
        bat 'if not exist output mkdir output'
        // Copiar todos los .html al directorio output
        bat 'xcopy /E /I /Y *.html output\\'
      }
    }
  }

  post {
    always {
      publishHTML target: [
        reportDir:    'output',
        reportFiles:  'index.html',
        reportName:   'Sitio HTML'
      ]
    }
  }
}

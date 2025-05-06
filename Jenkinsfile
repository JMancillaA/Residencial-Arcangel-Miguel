pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Publish HTML') {
      steps {
        // Sólo copia los archivos .html a la carpeta de salida
        sh 'mkdir -p output && cp -r **/*.html output/'
      }
    }
  }
  post {
    always {
      publishHTML (target: [
        reportDir: 'output',
        reportFiles: 'index.html',
        reportName: 'Sitio HTML'
      ])
    }
  }
}
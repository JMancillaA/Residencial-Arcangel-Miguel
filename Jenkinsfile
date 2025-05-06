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
        // Crear carpeta de salida si no existe
        bat 'if not exist output mkdir output'
        // Buscar recursivamente .html y copiarlos a output
        bat '''
          for /R %%F in (*.html) do (
            echo Copiando %%~nxF
            copy /Y "%%F" output\\
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

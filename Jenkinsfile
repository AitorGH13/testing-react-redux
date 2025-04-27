pipeline {
  agent any

  stages {
    stage ('Build') {
      steps {
        echo '--- Checkout del código fuente ---'
        checkout scm

        echo '--- Instalando dependencias con npm ---'
        sh 'npm install'

        echo '--- Compilando el proyecto con npm ---'
        sh 'npm run build'
      }
      stage('Testing') {
            steps {
                echo '--- Ejecutando pruebas con npm ---'
                sh 'npm test'
            }
        }
    }

    post {
        always {
            echo '--- Archivando resultados de pruebas ---'
            // Ajusta la ruta según tu configuración de reportes de pruebas
            junit '**/test-results/*.xml'
        }
        success {
            echo '### Build y tests correctos en rama feature'
        }
        failure {
            echo '!!! Ha fallado el pipeline en rama feature'
        }
    }
}

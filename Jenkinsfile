pipeline {
    // Usar contenedor Docker con Node.js y Python preinstalados
    agent {
        docker {
            image 'node:18-buster'
            args '--user root'
        }
    }

    stages {
        stage('Build') {
            when { branch 'feature' }
            steps {
                echo '--- Instalando dependencias del sistema ---'
                sh 'apt-get update && apt-get install -y python2 build-essential'

                echo '--- Checkout del código fuente ---'
                checkout scm

                echo '--- Instalando dependencias con npm ---'
                sh 'npm install'

                echo '--- Compilando el proyecto con npm ---'
                sh 'npm run build'
            }
        }

        stage('Testing') {
            when { branch 'feature' }
            steps {
                echo '--- Ejecutando pruebas con npm ---'
                sh 'npm test'
            }
        }
    }

    post {
        always {
            echo '--- Archivando resultados de pruebas ---'
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

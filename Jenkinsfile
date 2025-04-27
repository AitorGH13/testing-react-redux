pipeline {
    // Ejecutar en cualquier nodo disponible que tenga sudo
    agent any

    // Definir variables de entorno para node-gyp
    environment {
        PYTHON = '/usr/bin/python2'
    }

    stages {
        stage('Prepare') {
            when { branch 'feature' }
            steps {
                echo '--- Instalando herramientas de compilación y Python2 ---'
                // Requiere que el nodo Jenkins permita sudo sin contraseña
                sh 'sudo apt-get update && sudo apt-get install -y python2 build-essential'
            }
        }

        stage('Checkout') {
            when { branch 'feature' }
            steps {
                echo '--- Checkout del código fuente ---'
                checkout scm
            }
        }

        stage('Build') {
            when { branch 'feature' }
            steps {
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


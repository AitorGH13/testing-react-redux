pipeline {
    // Ejecutar en cualquier nodo disponible
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Usar contenedor Docker para Node.js y Python
                    def img = docker.image('node:18-buster')
                    img.pull()
                    img.inside('--user root') {
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
            }
        }

        stage('Testing') {
            steps {
                script {
                    // Ejecutar tests dentro del mismo contenedor
                    docker.image('node:18-buster').inside('--user root') {
                        echo '--- Ejecutando pruebas con npm ---'
                        sh 'npm test'
                    }
                }
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

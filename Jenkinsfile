pipeline {
    agent any

    // Sólo ejecutar en la rama "feature"
    triggers {
        pollSCM('H/5 * * * *')  // opcional: escanea Git cada 5 minutos
    }

    options {
        // Mantener sólo los últimos 10 builds
        buildDiscarder(logRotator(numToKeepStr: '10'))
        // Limitar el tiempo máximo de ejecución a 1 hora
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Checkout') {
            when {
                branch 'feature'
            }
            steps {
                // Asume que el repositorio ya está configurado en el job
                checkout scm
            }
        }

        stage('Build') {
            when {
                branch 'feature'
            }
            steps {
                echo '🔨 Compilando el proyecto...'
                // Ejemplo para un proyecto Java/Maven:
                sh 'mvn clean compile'
                // O para Node.js:
                // sh 'npm install'
                // sh 'npm run build'
            }
        }

        stage('Testing') {
            when {
                branch 'feature'
            }
            steps {
                echo '✅ Ejecutando pruebas...'
                // Ejemplo para un proyecto Java/Maven:
                sh 'mvn test'
                // O para Node.js:
                // sh 'npm test'
            }
            post {
                always {
                    // Publica resultados de pruebas JUnit, si aplica
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }

        // Puedes añadir más etapas si lo deseas:
        // stage('Code Analysis') { ... }
        // stage('Package') { ... }
        // stage('Deploy to staging') { ... }
    }

    post {
        success {
            echo '🎉 Build y tests completados correctamente en rama feature.'
        }
        failure {
            echo '❌ Algo falló en la build o en los tests.'
        }
        always {
            echo '📦 Fin del pipeline.'
        }
    }
}

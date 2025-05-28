pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Checkout') {
            when {
                branch 'feature'
            }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            when {
                branch 'feature'
            }
            steps {
                echo '🔨 Compilando el proyecto...'
                sh 'mvn clean compile'
            }
        }

        stage('Testing') {
            when {
                branch 'feature'
            }
            steps {
                echo '✅ Ejecutando pruebas...'
                sh 'mvn test'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                }
            }
        }
    }
}

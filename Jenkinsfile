pipeline {
    agent any

    environment {
        // Ajusta estos valores a tu entorno
        ASSIGN_HOST       = 'asignatura.example.com'
        DEPLOY_PATH       = '/opt/asignatura/app'
        SSH_CREDENTIALS   = 'asignatura-ssh-key'
        DOCKER_IMAGE      = 'myapp:latest'
        DOCKER_CONTAINER  = 'myapp-container'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 1, unit: 'HOURS')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Compilando el proyecto...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Testing') {
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

        stage('Deploy to Assignment Container') {
            steps {
                echo "📦 Desplegando en ${ASSIGN_HOST}:${DEPLOY_PATH}"
                sshagent([SSH_CREDENTIALS]) {
                    sh """
                       scp -o StrictHostKeyChecking=no \
                         target/myapp.jar \
                         ${ASSIGN_HOST}:${DEPLOY_PATH}/
                       ssh -o StrictHostKeyChecking=no ${ASSIGN_HOST} \\
                         "cd ${DEPLOY_PATH} && \
                          pkill -f myapp.jar || true && \
                          nohup java -jar myapp.jar > app.log 2>&1 &"
                    """
                }
            }
        }

        stage('Deploy Docker inside Assignment Container') {
            steps {
                echo "🐳 Desplegando Docker en ${ASSIGN_HOST}"
                sshagent([SSH_CREDENTIALS]) {
                    sh """
                       ssh -o StrictHostKeyChecking=no ${ASSIGN_HOST} \\
                         "cd ${DEPLOY_PATH} && \
                          docker build -t ${DOCKER_IMAGE} . && \
                          docker rm -f ${DOCKER_CONTAINER} || true && \
                          docker run -d --name ${DOCKER_CONTAINER} -p 8080:80 ${DOCKER_IMAGE}"
                    """
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Despliegue completo en main.'
        }
        failure {
            echo '❌ Ha ocurrido un fallo en alguna etapa.'
        }
        always {
            echo '📦 Fin del pipeline.'
        }
    }
}

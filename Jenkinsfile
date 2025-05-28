pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') 
    }

    environment {
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
            when { branch 'main' }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            when { branch 'main' }
            steps {
                echo '🔨 Compilando el proyecto...'
                sh 'mvn clean package -DskipTests'   
            }
        }

        stage('Testing') {
            when { branch 'main' }
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
            when { branch 'main' }
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
            when { branch 'main' }
            steps {
                echo "🐳 Desplegando Docker dentro de ${ASSIGN_HOST}"
                sshagent([SSH_CREDENTIALS]) {
                    sh """
                       ssh -o StrictHostKeyChecking=no ${ASSIGN_HOST} \\
                         "cd ${DEPLOY_PATH} && \
                          # Reconstruir imagen Docker a partir del Dockerfile si procede
                          docker build -t ${DOCKER_IMAGE} . && \
                          # Reemplazar contenedor existente
                          docker rm -f ${DOCKER_CONTAINER} || true && \
                          docker run -d \\
                            --name ${DOCKER_CONTAINER} \\
                            -p 8080:80 \\
                            ${DOCKER_IMAGE}"
                    """
                }
            }
        }
    }
}


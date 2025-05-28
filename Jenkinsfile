pipeline {
    agent any

    environment {
        ASSIGN_HOST      = 'asignatura.example.com'
        DEPLOY_PATH      = '/opt/asignatura/app'
        SSH_CREDENTIALS  = 'asignatura-ssh-key'
        DOCKER_IMAGE     = 'myapp:latest'
        DOCKER_CONTAINER = 'myapp-container'
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

        stage('Install') {
            steps {
                echo '📦 Instalando dependencias con Docker…'
                // Monta el workspace en /app y corre npm ci dentro de node:16
                sh '''
                  docker run --rm \
                    -v "${PWD}":/app \
                    -w /app \
                    node:16-alpine \
                    npm ci
                '''
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Compilando la app con Docker…'
                sh '''
                  docker run --rm \
                    -v "${PWD}":/app \
                    -w /app \
                    node:16-alpine \
                    npm run build
                '''
            }
        }

        stage('Testing') {
            steps {
                echo '✅ Ejecutando tests con Docker…'
                sh '''
                  docker run --rm \
                    -v "${PWD}":/app \
                    -w /app \
                    node:16-alpine \
                    npm test -- --ci --reporters=default
                '''
            }
        }

        stage('Deploy to Assignment Container') {
            steps {
                echo "🚀 Desplegando build a ${ASSIGN_HOST}:${DEPLOY_PATH}"
                sshagent([SSH_CREDENTIALS]) {
                    sh '''
                      scp -o StrictHostKeyChecking=no \
                        -r build/* \
                        ${ASSIGN_HOST}:${DEPLOY_PATH}/
                      ssh -o StrictHostKeyChecking=no ${ASSIGN_HOST} "
                        pkill -f 'serve -s ${DEPLOY_PATH}' || true &&
                        nohup npx serve -s ${DEPLOY_PATH} > serve.log 2>&1 &
                      "
                    '''
                }
            }
        }

        stage('Deploy Docker inside Assignment Container') {
            steps {
                echo "🐳 Armando y lanzando Docker en ${ASSIGN_HOST}"
                sshagent([SSH_CREDENTIALS]) {
                    sh '''
                      ssh -o StrictHostKeyChecking=no ${ASSIGN_HOST} "
                        cd ${DEPLOY_PATH} &&
                        cat > Dockerfile << 'EOF'
                        FROM nginx:alpine
                        COPY . /usr/share/nginx/html
                        EOF &&
                        docker build -t ${DOCKER_IMAGE} . &&
                        docker rm -f ${DOCKER_CONTAINER} || true &&
                        docker run -d --name ${DOCKER_CONTAINER} -p 8080:80 ${DOCKER_IMAGE}
                      "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline finalizado correctamente.'
        }
        failure {
            echo '❌ Ha fallado alguna etapa.'
        }
        always {
            echo '📦 Fin del pipeline.'
        }
    }
}

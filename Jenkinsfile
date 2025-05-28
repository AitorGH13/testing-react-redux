pipeline {
    agent any

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
                echo '📦 Instalando dependencias…'
                sh 'npm ci'
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Generando build de producción…'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                echo '✅ Ejecutando tests…'
                sh 'npm test -- --ci'
            }
        }

        stage('Deploy to Assignment Container') {
            steps {
                echo '🚀 Desplegando build en el contenedor de la asignatura…'
                sshagent(['asignatura-ssh-key']) {
                    sh '''
                      scp -o StrictHostKeyChecking=no -r build/* \
                        admin@asignatura.example.com:/opt/asignatura/app/

                      ssh -o StrictHostKeyChecking=no admin@asignatura.example.com "
                        pkill -f 'npx serve -s /opt/asignatura/app' || true &&
                        nohup npx serve -s /opt/asignatura/app > serve.log 2>&1 &
                      "
                    '''
                }
            }
        }

        stage('Deploy Docker inside Assignment Container') {
            steps {
                echo '🐳 Desplegando contenedor Docker dentro del contenedor de la asignatura…'
                sshagent(['asignatura-ssh-key']) {
                    sh '''
                      ssh -o StrictHostKeyChecking=no admin@asignatura.example.com "
                        cd /opt/asignatura/app &&
                        cat > Dockerfile << 'EOF'
                        FROM nginx:alpine
                        COPY . /usr/share/nginx/html
                        EOF &&
                        docker build -t myapp:latest . &&
                        docker rm -f myapp-container || true &&
                        docker run -d --name myapp-container -p 8080:80 myapp:latest
                      "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '🎉 ¡Pipeline completado con éxito!'
        }
        failure {
            echo '❌ Ha fallado alguna etapa.'
        }
        always {
            echo '📦 Fin del pipeline.'
        }
    }
}


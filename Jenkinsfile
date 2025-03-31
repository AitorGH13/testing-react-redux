pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS 21'  // Si usas Node.js, asegúrate de que esta herramienta esté configurada en Jenkins
    }
    
    stages {
        stage("Checkout") {
            steps {
                git branch: 'feature/mejorar-documentacion', 
                    url: 'https://github.com/AitorGH13/testing-react-redux.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }
        stage('Testing') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh 'npm test'
                    }
                }
            }
        }
        stage('Deploy en el contenedor de la asignatura') {
            steps {
                sh 'npm run build'
                sh 'sudo cp -r build /var/www/html/'
            }
        }
        stage('Deploy en Docker dentro del contenedor') {
            steps {
                sh '''
                docker build -t mi-app .
                docker run -d -p 3000:3000 --name app-container mi-app
                '''
            }
        }
    }
}

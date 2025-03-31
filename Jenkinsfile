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
                sh 'npm test'
            }
        }
    }
}

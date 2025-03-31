pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS 18'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'feature/mejorar-documentacion', 
                    url: 'https://github.com/AitorGH13/testing-react-redux.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install --force'
            }
        }
        stage('Testing') {
            steps {
                sh 'npm test'
            }
        }
    }
}

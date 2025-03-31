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
                sh 'np outdated'
                sh 'npm update'
                sh 'npm cache clean --force'
                sh 'npm install --legacy-peer-deps'
            }
        }
        stage('Testing') {
            steps {
                sh 'npm test'
            }
        }
    }
}

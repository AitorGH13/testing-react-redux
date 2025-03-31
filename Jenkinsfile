pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS 16'
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
                sh 'npm cache clean --force'
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

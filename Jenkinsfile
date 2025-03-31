pipeline {
    agent any

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

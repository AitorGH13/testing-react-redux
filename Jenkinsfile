pipeline {
    agent any
    tools {
        nodejs 'NodeJS 16'
        maven 'Maven 3.8.1'  // Si estás usando Maven en algún paso, por ejemplo
    }

    stages {
        stage("Checkout") {
            steps {
                git branch: 'feature/mejorar-documentacion', 
                    url: 'https://github.com/AitorGH13/testing-react-redux.git'
            }
        }
        
        stage("Build") {
            steps {
                sh 'npm install'
                sh 'mvn -v'  // Esto es un ejemplo, si estás usando Maven
            }
        }

        stage("Testing") {
            parallel {
                stage("Unit Tests") {
                    agent { docker 'openjdk:7-jdk-alpine' }
                    steps {
                        sh 'java -version'
                    }
                }
                stage("Functional Tests") {
                    agent { docker 'openjdk:8-jdk-alpine' }
                    steps {
                        sh 'java -version'
                    }
                }
                stage("Integration Tests") {
                    steps {
                        sh 'java -version'
                    }
                }
            }
        }

        stage("Deploy") {
            steps {
                echo "Deploy!"
            }
        }
    }
}

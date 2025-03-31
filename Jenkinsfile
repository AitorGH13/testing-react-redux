pipeline {
    agent any
    tools {
        nodejs 'NodeJS 16'  // Si estás utilizando NodeJS, este es necesario
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
            }
        }

        stage("Testing") {
            parallel {
                stage("Unit Tests") {
                    agent { docker { image 'openjdk:7-jdk-alpine' } }
                    steps {
                        sh 'java -version'
                    }
                }
                stage("Functional Tests") {
                    agent { docker { image 'openjdk:8-jdk-alpine' } }
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

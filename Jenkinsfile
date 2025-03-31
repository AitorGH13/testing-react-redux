pipeline {
    agent any
    tools {
        nodejs 'NodeJS 16'  // Si usas Node.js, asegúrate de que esta herramienta esté configurada en Jenkins
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
                    steps {
                        script {
                            docker.image('openjdk:7-jdk-alpine').inside {
                                sh 'java -version'
                            }
                        }
                    }
                }
                stage("Functional Tests") {
                    steps {
                        script {
                            docker.image('openjdk:8-jdk-alpine').inside {
                                sh 'java -version'
                            }
                        }
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

pipeline {
    agent any

    environment {
        REGISTRY = 'barathkumar29/java-microservice'
        IMAGE_TAG = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                dir('') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                dir('') {
                    script {
                        docker.build("${REGISTRY}:${IMAGE_TAG}")
                        docker.withRegistry('', 'docker-hub-credentials') {
                            docker.image("${REGISTRY}:${IMAGE_TAG}").push()
                        }
                    }
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                dir('') {
                    sh """
                        kubectl apply -f k8s/deployment.yaml
                        kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
}

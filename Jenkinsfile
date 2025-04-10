pipeline {
    agent any

    environment {
        REGISTRY = 'hruthingali/java-microservice'
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
                dir('java-microservice') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Docker Build & Push') {
            when {
                branch 'develop'
            }
            steps {
                dir('java-microservice') {
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
            when {
                branch 'develop'
            }
            steps {
                dir('java-microservice') {
                    sh """
                        sed -i 's|IMAGE_TAG|${IMAGE_TAG}|g' k8s/deployment.yaml
                        kubectl apply -f k8s/ --validate=false
                    """
                }
            }
        }
    }
}

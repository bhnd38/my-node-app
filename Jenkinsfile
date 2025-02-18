pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "docker.io/hyeokbin"
        DOCKER_IMAGE = "my-node-app"
        IMAGE_TAG = "latest"
        KUBE_CONTEXT = "kubernetes-admin@kubernetes"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: 'jenkins-token', url: 'https://github.com/bhnd38/my-node-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl --context=${KUBE_CONTEXT} apply -f deployment.yaml"
                    sh "kubectl --context=${KUBE_CONTEXT} apply -f service.yaml"
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

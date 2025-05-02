pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = "docker.io"
        DOCKER_IMAGE = "jeyz9/springboot-demo"
        K8S_DEPLOYMENT_FILE = "k8s/deployment.yaml"
    }
    
    stages {
        stage('Clone Repository') {
            steps {
                // checkout scm
                // git branch: 'main', url: 'https://github.com/JeyZ9/simple-spring.git'
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/JeyZ9/simple-spring.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest")
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

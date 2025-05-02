pipeline {
    agent any
    
    environment {
        // DOCKER_REGISTRY = "docker.io"
        DOCKER_IMAGE = "jeyz9/springboot-demo"
        DOCKER_TAG = "Latest"
        K8S_DEPLOYMENT_FILE = "k8s/deployment.yaml"
    }
    
    stages {
        stage('Clone') {
            steps {
                // checkout scm
                git branch: 'main', url: 'https://github.com/JeyZ9/simple-spring.git'
                // git branch: 'main'
                // url: 'https://github.com/JeyZ9/simple-spring.git'
            }
        }
        
        stage('Build') {
            steps {
                script {
                    // docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest")
                    // docker.build("jeyz9/springboot-demo:latest")
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                // script {
                //     docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                //         docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:latest").push()
                //     }
                // }
                withCredentials([usernamePassword(credentialsId: 'dockerhub_credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE}"
                    sh "kubectl apply -f ."
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

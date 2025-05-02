pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jeyz9/springboot-demo"
        DOCKER_TAG = "Latest"
        K8S_DEPLOYMENT_FILE = "k8s/deployment.yaml"
        KUBECONFIG = '/path/to/kubeconfig'
    }
    
    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/JeyZ9/simple-spring.git'
            }
        }
        
        stage('Build') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                }
            }
        }
        
        stage('Test') {
            steps {
                withCredentials([usernamePassword(credentialsId: '26ad3f7e-7b0a-404f-9809-3e121155b76f', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh "export KUBECONFIG=${KUBECONFIG} && kubectl apply -f ${K8S_DEPLOYMENT_FILE} --validate=false"
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

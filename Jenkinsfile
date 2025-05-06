pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jeyz9/springboot-demo"
        DOCKER_TAG = "latest"
        K8S_DEPLOYMENT_FILE = "k8s/deployment.yaml"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/JeyZ9/simple-spring.git'
            }
        }

        stage('Build Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "5eba72a1-239e-4534-8fc0-4d12515a4159", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        stage('Deploy to K8s') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                        export KUBECONFIG=$KUBECONFIG
                        kubectl apply -f ${K8S_DEPLOYMENT_FILE} --validate=false
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment succeeded!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}

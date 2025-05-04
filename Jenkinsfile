pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "jeyz9/springboot-demo"
        DOCKER_TAG = "Latest"
        K8S_DEPLOYMENT_FILE = "k8s/deployment.yaml"
        KUBECONFIG = '/home/vagrant/.kube/config'
        // DOCKER_ID = "5eba72a1-239e-4534-8fc0-4d12515a4159"
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
                withCredentials([usernamePassword(credentialsId: "5eba72a1-239e-4534-8fc0-4d12515a4159", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Debug K8s Context') {
            steps {
                sh "export KUBECONFIG=${KUBECONFIG} && kubectl config current-context || echo 'Config not working'"
            }
        }

                stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "5eba72a1-239e-4534-8fc0-4d12515a4159", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Prepare Kubeconfig') {
            steps {
                sh '''
                    mkdir -p ~/.kube
                    cp -f $(minikube kubeconfig) ~/.kube/config
                    chmod 600 ~/.kube/config
                '''
            }
        }

        stage('Deploy') {
            steps {
                // script {
                //     sh "export KUBECONFIG=${KUBECONFIG} && kubectl apply -f ${K8S_DEPLOYMENT_FILE} --validate=false"
                // }
                // withEnv(["KUBECONFIG=${KUBECONFIG}"]) {
                //     sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE} --validate=false"
                // }
                        // sh '''
                        //     chmod 644 /home/vagrant/.kube/config || true
                        //     export KUBECONFIG=/home/vagrant/.kube/config
                        // '''

                withEnv(["KUBECONFIG=/var/lib/jenkins/.kube/config"]) {
                    sh "kubectl apply -f ${K8S_DEPLOYMENT_FILE} --validate=false"
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

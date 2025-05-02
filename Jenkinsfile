pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t demo-app:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                sh 'docker tag demo-app:latest your_dockerhub_username/demo-app:latest'
                sh 'docker push your_dockerhub_username/demo-app:latest'
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
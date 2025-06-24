pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/orders-service:latest'
        KUBE_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-repo/orders-service.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_PASSWORD')]) {
                    sh '''
                        echo $DOCKER_PASSWORD | docker login -u your-dockerhub-username --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml -n $KUBE_NAMESPACE'
                sh 'kubectl rollout restart deployment orders-service -n $KUBE_NAMESPACE'
            }
        }
    }

    post {
        failure {
            mail to: 'devops-team@example.com',
                 subject: "🚨 Jenkins Pipeline Failed",
                 body: "Check the console log: ${env.BUILD_URL}"
        }
    }
}

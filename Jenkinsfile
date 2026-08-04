pipeline {

    agent any

    environment {
        IMAGE_NAME = "vijayvs6383/static-website"
        KUBECONFIG = "/var/jenkins_home/.kube/config-jenkins"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/
                kubectl rollout restart deployment/static-website
                kubectl rollout status deployment/static-website
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get deployments
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }
}

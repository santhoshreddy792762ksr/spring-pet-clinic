pipeline {
    agent any

    environment {
        IMAGE_NAME = "santhoshreddyksr/spring-pet-clinic"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://https://github.com/santhoshreddy792762ksr/spring-pet-clinic.git'
            }
        }

        stage('building with maven') {
            steps {
                sh'''
                  mvn clean package -DskipTests
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                  docker push $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'jenkins-minikube']) {
                   sh 'kubectl version --client'
                   sh 'kubectl apply -f k8s/deployment.yaml'
                   sh 'kubectl apply -f k8s/service.yaml'
                }  
            }
        }

    }
}

/* groovylint-disable CompileStatic, DuplicateStringLiteral, NestedBlockDepth */
pipeline {
    agent any

    stages {

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_REGISTRY = 'neroxxpips'
                DOCKER_IMAGE_NAME = 'oe-nodejs'
                DOCKER_IMAGE_TAG = 'latest'
                DOCKERHUB_CREDENTIALS = credentials('dockerhub')
                DOCKER_PASSWORD = credentials('docker-token')
                DOCKER_USERNAME = 'neroxxpips'
            }
            steps {
                sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG .'
                sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
                sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:$DOCKER_IMAGE_TAG'
                sh 'docker logout'
            }
        }

        stage('Integrate Jenkins with EKS Cluster and Deploy App') {
            environment {
                EKS_CLUSTER_NAME = 'OE-DevOps-cluster'
                AWS_REGION = 'us-east-1'
            }
            steps {
                withAWS(credentials: 'aws-credentials', region: $AWS_REGION) {
                    script {
                        sh('aws eks update-kubeconfig --name $EKS_CLUSTER_NAME --region $AWS_REGION')
                        sh 'kubectl apply -f deploy/deployment.yaml -f deploy/service.yaml'
                    }
                }
            }
        }
    }
}

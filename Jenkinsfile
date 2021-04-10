env.DOCKER_REGISTRY = 'hansluber'
env.DOCKER_IMAGE_NAME = 'backend5'
pipeline {
    agent any
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        } 
        stage('Git Pull from Github') {
            steps {
                git branch: 'main', url: 'https://github.com/hansluber/backend-mernproject.git'
            }
        }    
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER} ."
            }
        }              
        stage('Push Docker Image to ECR') {
            steps {
                sh "docker push $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
            }
        } 
        stage('Deploy To Kubernetes Cluster') {
            steps {
                sh'''sed -i "15d" backend-deployment.yml'''
                sh'''sed -i "14 a \'\\'          image: $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}" backend-deployment.yml && sed -i "s/''//" backend-deployment.yml'''
                sh "kubectl apply -f backend-deployment.yml"                
            }
        }
        stage('Remove Docker Image in local') {
            steps {
                sh "docker rmi $DOCKER_REGISTRY/$DOCKER_IMAGE_NAME:${BUILD_NUMBER}"
            }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }        
    }
}

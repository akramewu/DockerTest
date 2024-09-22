@Library(['piper-lib']) _

pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker-registry-credentials')
        DOCKER_IMAGE = 'akramulislam/test-image'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    def dockerfilePath = './Dockerfile'  // Adjust if your Dockerfile is in a different location
                    def imageName = "${DOCKER_IMAGE}"
                    def imageTag = "${DOCKER_TAG}"
                    def dockerConfigJsonCredentialsId = 'docker-registry-credentials'
                    
                    kanikoExecute(
                        script: this,
                        dockerfilePath: dockerfilePath,
                        buildOptions: ['--destination=' + imageName + ':' + imageTag],
                        containerImageNameAndTag: imageName + ':' + imageTag,
                        dockerConfigJsonCredentialsId: dockerConfigJsonCredentialsId
                    )
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}

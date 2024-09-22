pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('591167b9-f7b9-494d-8270-690ddea43cfb')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/akramewu/DockerTest.git'
            }
        }
        stage('Build Container Image') {
            steps {
                script {
                    sh 'sudo /usr/bin/podman build -t dockertest:${BUILD_NUMBER} .'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | sudo /usr/bin/podman login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin docker.io'
                    sh 'sudo /usr/bin/podman tag dockertest:${BUILD_NUMBER} $DOCKERHUB_CREDENTIALS_USR/dockertest:${BUILD_NUMBER}'
                    sh 'sudo /usr/bin/podman push $DOCKERHUB_CREDENTIALS_USR/dockertest:${BUILD_NUMBER}'
                    sh 'sudo /usr/bin/podman logout docker.io'
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

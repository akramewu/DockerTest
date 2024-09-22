pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'akramulislam/test-image'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        SLIM_HOST_ADDRESS = "35.195.9.232"
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
                    withCredentials([usernamePassword(credentialsId: 'docker-akramul-personal', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Copy Dockerfile to slim host
                        sh "scp -o StrictHostKeyChecking=no -i \"${WORKSPACE}/SLV_keypair_IRE.pem\" Dockerfile emroot@${SLIM_HOST_ADDRESS}:/tmp/Dockerfile"
                        
                        // Build and push Docker image on slim host
                        sh """
                            ssh -o StrictHostKeyChecking=no -i "${WORKSPACE}/SLV_keypair_IRE.pem" emroot@"${SLIM_HOST_ADDRESS}" "sudo su - root -c '
                                cd /tmp
                                docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                                echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin
                                docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                                docker logout
                                rm Dockerfile
                            '"
                        """
                    }
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

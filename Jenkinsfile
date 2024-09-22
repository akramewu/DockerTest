@Library(['piper-lib']) _

pipeline {
    agent any
    
    environment {
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
                    
                    withCredentials([usernamePassword(credentialsId: 'docker-akramul-personal', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Create a temporary Docker config.json file
                        sh """
                            echo '{"auths":{"https://index.docker.io/v1/":{"auth":"'"\$(echo -n "${DOCKER_USER}:${DOCKER_PASS}" | base64)"'"}}}' > ${WORKSPACE}/docker-config.json
                        """
                        
                        kanikoExecute(
                            script: this,
                            dockerfilePath: dockerfilePath,
                            buildOptions: ['--destination=' + imageName + ':' + imageTag],
                            containerImageNameAndTag: imageName + ':' + imageTag,
                            dockerConfigJsonPath: "${WORKSPACE}/docker-config.json"
                        )
                        
                        // Clean up the temporary Docker config file
                        sh "rm ${WORKSPACE}/docker-config.json"
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

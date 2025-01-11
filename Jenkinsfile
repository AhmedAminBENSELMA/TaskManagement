pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') // Poll SCM every 5 minutes
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub') // Jenkins credentials ID for Docker Hub
        IMAGE_NAME_SERVER = 'ahmedaminebenselma/mern-server' // Replace with your Docker Hub username
        IMAGE_NAME_CLIENT = 'ahmedaminebenselma/mern-client' // Replace with your Docker Hub username
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    echo 'Starting Git checkout...'
                    git branch: 'main',
                        url: 'https://github.com/AhmedAminBENSELMA/TaskManagement.git',
                        credentialsId: 'github' // Jenkins credentials ID for GitHub SSH key
                }
            }
        }

        stage('Build Server Image') {
            steps {
                script {
                    echo 'Building server image...'
                    sh """
                        docker build -t ${IMAGE_NAME_SERVER} ./server
                    """
                }
            }
        }

        stage('Build Client Image') {
            steps {
                script {
                    echo 'Building client image...'
                    sh """
                        docker build -t ${IMAGE_NAME_CLIENT} ./client
                    """
                }
            }
        }

        stage('Scan Server Image') {
            steps {
                script {
                    echo 'Scanning server image...'
                    sh """
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                            aquasec/trivy:latest image --exit-code 0 \
                            --severity LOW,MEDIUM,HIGH,CRITICAL \
                            ${IMAGE_NAME_SERVER}
                    """
                }
            }
        }

        stage('Scan Client Image') {
            steps {
                script {
                    echo 'Scanning client image...'
                    sh """
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                            aquasec/trivy:latest image --exit-code 0 \
                            --severity LOW,MEDIUM,HIGH,CRITICAL \
                            ${IMAGE_NAME_CLIENT}
                    """
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    echo 'Pushing images to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh """
                            echo "${PASSWORD}" | docker login -u "${USERNAME}" --password-stdin
                            docker push ${IMAGE_NAME_SERVER}:latest
                            docker push ${IMAGE_NAME_CLIENT}:latest
                        """
                    }
                }
            }
        }
    }
}

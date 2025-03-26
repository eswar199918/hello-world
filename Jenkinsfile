pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "eswar199918/hello-world"
        IMAGE_TAG = "${BUILD_NUMBER}"  // Versioning with build number
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/eswar199918/hello-world.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$IMAGE_TAG -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
                    sh 'docker push $DOCKER_IMAGE:$IMAGE_TAG'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    def deploymentExists = sh(
                        script: "kubectl get deployment hello-world --ignore-not-found",
                        returnStatus: true
                    ) == 0

                    if (!deploymentExists) {
                        sh 'kubectl apply -f deployment.yaml'
                    }

                    sh 'kubectl set image deployment/hello-world hello-world=$DOCKER_IMAGE:$IMAGE_TAG --record'
                }
            }
        }
    }
}


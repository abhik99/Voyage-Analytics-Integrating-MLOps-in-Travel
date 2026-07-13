pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "travel-api"
        DOCKER_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
            }
        }

        stage('Build Image') {
            steps {
                echo 'Building Docker image...'
                // CHANGE 'sh' TO 'bat' FOR WINDOWS
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Push Image') {
            steps {
                echo 'Pushing Docker image to registry...'
                // bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes...'
                // bat "kubectl apply -f deployment.yml"
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. Please check logs.'
        }
    }
}

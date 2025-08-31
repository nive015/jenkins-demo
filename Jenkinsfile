pipeline {
    agent any 
    environment {
        // Reference to Jenkins credentials (must exist in Jenkins → Credentials with ID = test1)
        DOCKERHUB_CREDENTIALS = credentials('test1')
    }
    stages {
        stage('Build docker image') {
            steps {  
                echo "Building Docker image..."
                sh 'docker build -t nive015/jenkins-demo:$BUILD_NUMBER .'
            }
        }
        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'test1', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    echo "Logging in to Docker Hub..."
                    sh """
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                    """
                }
            }
        }
        stage('Push image') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push nive015/jenkins-demo:$BUILD_NUMBER'
            }
        }
        stage('Deploy to Staging') {
            steps {
                echo "Deploying container..."
                // Pull the latest image from Docker Hub
                sh 'docker pull nive015/jenkins-demo:$BUILD_NUMBER'

                // Stop and remove any existing containers
                sh 'docker stop myapp-container || true'
                sh 'docker rm myapp-container || true'

                // Run the new container with the latest image
                sh 'docker run -d --name myapp-container -p 8000:8000 nive015/jenkins-demo:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            echo "Logging out from Docker Hub..."
            sh 'docker logout'
        }
    }
}

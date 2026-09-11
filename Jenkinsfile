pipeline {
    agent any

    environment {
        IMAGE_NAME = 'system-health-dashboard'
        CONTAINER_NAME = "health-check-${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out project...'
                // your existing checkout code
            }
        }

        stage('Install') {
            steps {
                echo 'Installing dependencies...'
                sh '''
                    pip3 install -r requirements.txt
                    pip3 install pytest
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    pytest -v
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t ${IMAGE_NAME}:latest .
                '''
            }
        }

        stage('Tag') {
            steps {
                echo "Tagging image with build number ${BUILD_NUMBER}"
                sh '''
                    docker tag ${IMAGE_NAME}:latest ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health check') {
            steps {
                echo 'Checking application health...'
                sh '''
                    docker rm -f ${CONTAINER_NAME} 2>/dev/null || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p 5000:5000 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}

                    sleep 5

                    curl --fail http://localhost:5000/health

                    docker rm -f ${CONTAINER_NAME}
                '''
            }
        }

        // Keep your existing Push to Docker Hub stage here
    }
}

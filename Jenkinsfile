pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out project...'

                git branch: 'develop',
                    url: 'https://github.com/PriyanshiiV/System-health-dashboard.git'
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'

                sh '''
                    python3 -m pytest tests/test_app.py -v
                '''
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Running integration tests...'

                sh '''
                    python3 -m pytest tests/test_app.py -v
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh '''
                    docker build -t system-health-dashboard:1.0.0 .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker tag system-health-dashboard:1.0.0 \
                            "$DOCKER_USERNAME/system-health-dashboard:1.0.0"

                        docker push \
                            "$DOCKER_USERNAME/system-health-dashboard:1.0.0"

                        docker logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the stage logs for details.'
        }
    }
}

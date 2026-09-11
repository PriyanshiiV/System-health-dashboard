pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Install') {
            steps {
                echo 'Installing Python dependencies...'
                sh '''
                    python3 -m pip install --break-system-packages -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests...'
                sh '''
                    python3 -m pytest -v
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building Docker image...'
                sh '''
                    docker build -t system-health-dashboard:local .
                '''
            }
        }

        stage('Tag') {
            steps {
                echo "Tagging image with Jenkins build number: ${BUILD_NUMBER}"
                sh '''
                    docker tag system-health-dashboard:local system-health-dashboard:${BUILD_NUMBER}
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo 'Running application health check...'
                sh '''
                    docker rm -f system-health-check || true

                    docker run -d \
                        --name system-health-check \
                        -p 5001:5000 \
                        -e APP_ENV=production \
                        system-health-dashboard:${BUILD_NUMBER}

                    sleep 5

                    curl --fail http://localhost:5001/health

                    docker rm -f system-health-check
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }

        always {
            echo "Jenkins build number: ${BUILD_NUMBER}"
        }
    }
}
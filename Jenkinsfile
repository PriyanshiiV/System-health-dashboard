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
                sh 'python3 -m pytest tests/test_app.py -v'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Running integration tests...'
                sh 'python3 -m pytest tests/test_pipeline.py -v'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t system-health-dashboard:1.0.0 .'
            }
        }
    }
}
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                pip install pytest
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                . venv/bin/activate
                pytest -v
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Deploying application to staging..."
                mkdir -p deployment
                cp -r * deployment/
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
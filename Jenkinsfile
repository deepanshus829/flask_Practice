pipeline {
    agent any

    environment {
        MONGO_URI = "mongodb://mongo:27017/test_student_db"
        SECRET_KEY = "test-secret"
    }

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

        // 👇 Add this stage here
        stage('Verify Mongo') {
            steps {
                sh '''
                echo "Mongo URI = $MONGO_URI"

                echo "Checking DNS..."
                getent hosts mongo || true

                python3 - <<EOF
import socket
try:
    print("mongo resolves to:", socket.gethostbyname("mongo"))
except Exception as e:
    print("DNS Error:", e)
EOF
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
                echo "Deploying application..."

                rm -rf deployment
                mkdir deployment

                cp app.py deployment/
                cp requirements.txt deployment/
                cp docker-compose.yml deployment/
                cp -r templates deployment/

                echo "Deployment package created successfully."
                ls -R deployment
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
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'status-api'
        CONTAINER_NAME = 'status-api'
        PATH = "/usr/local/bin:/opt/homebrew/bin:/Applications/Docker.app/Contents/Resources/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker rm -f test-$CONTAINER_NAME || true
                    docker run -d --name test-$CONTAINER_NAME -p 3001:3000 $IMAGE_NAME:latest
                    sleep 5
                    curl -f http://localhost:3001/status
                    docker rm -f test-$CONTAINER_NAME
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f $CONTAINER_NAME || true
                    docker run -d --name $CONTAINER_NAME -p 3000:3000 $IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        success { echo 'Pipeline succeeded! API is live on port 3000.' }
        failure { echo 'Pipeline failed. Check the logs.' }
    }
}
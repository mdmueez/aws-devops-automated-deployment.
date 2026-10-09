
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Validate Website') {
            steps {
                sh '''
                    test -s index.html
                    test -f Dockerfile
                    echo "Website files validated"
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                      -t cloudops-dashboard:${BUILD_NUMBER} .
                    docker tag \
                      cloudops-dashboard:${BUILD_NUMBER} \
                      cloudops-dashboard:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f cloudops-dashboard || true

                    docker run -d \
                      --name cloudops-dashboard \
                      --restart unless-stopped \
                      -p 8081:80 \
                      cloudops-dashboard:latest
                '''
            }
        }

        stage('Verify Website') {
            steps {
                sh '''
                    sleep 3
                    curl --fail http://127.0.0.1:8081/
                    echo "Website deployment verified"
                '''
            }
        }
    }

    post {
        success {
            echo 'CloudOps deployment successful!'
        }
        failure {
            echo 'Deployment failed. Check the console output.'
        }
    }
}

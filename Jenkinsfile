pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ACCOUNT_ID = "679500837491"
        REPO_NAME = "my-web-app"
        ECR_URL = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-creds',
                    url: 'https://github.com/noreplymailre/my-devops-webapp.git',
                    branch: 'main'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} \
                        | docker login --username AWS --password-stdin ${ECR_URL}
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build -t ${REPO_NAME}:latest .
                    docker tag ${REPO_NAME}:latest ${ECR_URL}:latest
                """
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                sh """
                    docker push ${ECR_URL}:latest
                """
            }
        }
    }

    post {
        success {
            echo "🎉 Docker image successfully pushed to: ${ECR_URL}:latest"
        }
        failure {
            echo "❌ CI pipeline failed. Check logs!"
        }
    }
}

pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO_NAME = 'static_website'
        ECR_REGISTRY = '065786717905.dkr.ecr.us-east-1.amazonaws.com/vinayvarmavatsavai'
        IMAGE_TAG = 'latest'
        AWS_ACCESS_KEY_ID = "${AWS_ACCESS_KEY_ID}"
        AWS_SECRET_ACCESS_KEY = "${AWS_SECRET_ACCESS_KEY}"
    }

    stages {
        stage('Login to ECR') {
            steps {
                sh '''
                    echo "Logging in to AWS ECR..."
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                    aws configure set region $AWS_REGION
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPO_NAME:$IMAGE_TAG .'
            }
        }

        stage('Tag and Push to ECR') {
            steps {
                sh '''
                    docker tag $ECR_REPO_NAME:$IMAGE_TAG $ECR_REGISTRY/$ECR_REPO_NAME:$IMAGE_TAG
                    docker push $ECR_REGISTRY/$ECR_REPO_NAME:$IMAGE_TAG
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                    docker rm -f static_site_container || true
                    docker run -d -p 8299:80 --name static_site_container \
                    $ECR_REGISTRY/$ECR_REPO_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
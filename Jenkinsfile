pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = "834863651684"
        AWS_REGION     = "us-east-1"
        ECR_REPO       = "bookmyshow"
        IMAGE_TAG      = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Arun-Kumar-2405/Book-My-Show.git'
            }
        }

        stage('Install NPM Dependencies') {
            steps {
                echo "Installing Node.js dependencies..."
                dir('bookmyshow-app') {
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running tests if present..."
                dir('bookmyshow-app') {
                    sh 'npm test --if-present'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                dir('bookmyshow-app') {
                    sh """
                    docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                echo "Logging in to AWS ECR..."
                sh """
                aws ecr get-login-password --region ${AWS_REGION} \
                | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                """
            }
        }

        stage('Tag & Push Image to ECR') {
            steps {
                echo "Tagging image..."
                sh """
                docker tag ${ECR_REPO}:${IMAGE_TAG} \
                ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                """

                echo "Pushing image to ECR..."
                sh """
                docker push \
                ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to EKS using Ansible') {
            steps {
                echo "Deploying to EKS cluster via Ansible..."
                sh """
                ansible-playbook -i inventory deploy.yml
                """
            }
        }
    }
}

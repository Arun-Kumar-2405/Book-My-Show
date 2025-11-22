pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ECR_REPO = "834863651684.dkr.ecr.us-east-1.amazonaws.com/bookmyshow"
        IMAGE = "bookmyshow:latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Arun-Kumar-2405/Book-My-Show.git'
            }
        }

        stage('Run Tests') {
            steps {
                echo "Running TestNG tests..."
                sh './mvnw test'    // If you use Maven Wrapper
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE} ."
                }
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withAWS(credentials: 'aws-creds', region: AWS_REGION) {
                    sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin 834863651684.dkr.ecr.us-east-1.amazonaws.com
                    """
                }
            }
        }

        stage('Tag & Push Image to ECR') {
            steps {
                sh """
                docker tag ${IMAGE} ${ECR_REPO}
                docker push ${ECR_REPO}
                """
            }
        }

        stage('Deploy to EKS using Ansible') {
            steps {
                sshagent(credentials: ['ansible-ssh']) {
                    sh """
                    ansible-playbook /home/ubuntu/ansible/deploy.yml -i /home/ubuntu/ansible/inventory.ini
                    """
                }
            }
        }
    }
}


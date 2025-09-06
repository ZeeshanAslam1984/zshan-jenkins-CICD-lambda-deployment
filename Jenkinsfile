pipeline {
    agent any

    environment {
        AWS_REGION = "ap-southeast-2"
        S3_BUCKET  = "lambda-sam-artifacts-zeeshan"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ZeeshanAslam1984/zshan-jenkins-CICD-lambda-deployment.git'
            }
        }

        stage('Setup Environment') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install requests
                '''
            }
        }

        stage('Build SAM App') {
            steps {
                sh '''
                    . venv/bin/activate
                    sam build -t lambda-app/template.yaml
                '''
            }
        }

        stage('Package & Deploy') {
            steps {
                sh '''
                    sam package \
                        --template-file lambda-app/.aws-sam/build/template.yaml \
                        --s3-bucket $S3_BUCKET \
                        --output-template-file packaged.yaml \
                        --region $AWS_REGION

                    sam deploy \
                        --template-file packaged.yaml \
                        --stack-name lambda-sam-stack \
                        --capabilities CAPABILITY_IAM \
                        --region $AWS_REGION
                '''
            }
        }
    }
}

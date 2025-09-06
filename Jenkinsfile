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
                    if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
                '''
            }
        }

        stage('Install AWS & SAM CLI if missing') {
            steps {
                sh '''
                    if ! command -v aws &> /dev/null
                    then
                      echo "Installing AWS CLI..."
                      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                      unzip -o awscliv2.zip
                      sudo ./aws/install
                    fi

                    if ! command -v sam &> /dev/null
                    then
                      echo "Installing AWS SAM CLI..."
                      curl -Lo sam.zip https://github.com/aws/aws-sam-cli/releases/latest/download/aws-sam-cli-linux-x86_64.zip
                      unzip -o sam.zip -d sam-installation
                      sudo ./sam-installation/install
                    fi
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

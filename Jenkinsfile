pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
    }

    stages {

        stage('Setup') {
            steps {
                // Optional: create a virtual environment
                sh "python3 -m venv venv"
                sh ". venv/bin/activate && pip install --upgrade pip"
                sh ". venv/bin/activate && pip install -r lambda-app/tests/requirements.txt"
            }
        }

        stage('Test') {
            steps {
                sh ". venv/bin/activate && pytest lambda-app/tests --junitxml=results.xml"
                junit "results.xml"
            }
        }

        stage('Build') {
            steps {
                sh ". venv/bin/activate && sam build -t lambda-app/template.yaml"
            }
        }

        stage('Deploy') {
            steps {
                sh """
                . venv/bin/activate && sam deploy \
                    --stack-name my-lambda-stack \
                    --region ap-southeast-2 \
                    --no-confirm-changeset \
                    --no-fail-on-empty-changeset
                """
            }
        }

    }
}

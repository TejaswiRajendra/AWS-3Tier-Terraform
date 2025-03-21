pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_REGION            = 'us-east-1'
        S3_BUCKET_NAME        = 'tejaswirajendra-tfsbuck28081998'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/TejaswiRajendra/AWS-3Tier-Terraform.git'
            }
        }

        stage('Setup Terraform') {
            steps {
                sh 'terraform --version'
                sh 'terraform init'
            }
        }

        stage('Terraform Init with S3 Backend') {
            steps {
                sh '''
                terraform init -backend-config="bucket=${S3_BUCKET_NAME}" \
                               -backend-config="key=terraform.tfstate" \
                               -backend-config="region=${AWS_REGION}" \
                               -backend-config="encrypt=true"
                '''
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply') {
            when {
                branch 'master'  // Apply only on the master branch
            }
            steps {
                sh 'terraform apply -auto-approve tfplan'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'terraform.tfstate', fingerprint: true
        }
        success {
            echo 'Terraform execution successful!'
        }
        failure {
            echo 'Terraform execution failed!'
        }
    }
}

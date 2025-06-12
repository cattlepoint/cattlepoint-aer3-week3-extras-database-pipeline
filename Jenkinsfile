pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'     // adjust if needed
        ECR_REPO_NAME      = 'cattlepoint-database'
        DOCKER_CONTEXT_DIR = 'database'      // build context
    }

    stages {
        stage('Clean workspace') {
            steps { cleanWs() }
        }
        stage('Clone repo') {
            steps {
                sh 'git clone https://github.com/cattlepoint/cattlepoint-aer3-week2.git'
            }
        }

        stage('Resolve ECR URI') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'ecr-create'
                ]]) {
                    script {
                        env.ECR_URI = sh(
                            script: "aws ecr describe-repositories --repository-names $ECR_REPO_NAME --query 'repositories[0].repositoryUri' --output text",
                            returnStdout: true
                        ).trim()
                        env.ECR_REGISTRY = env.ECR_URI.tokenize('/')[0]
                    }
                }
            }
        }

        stage('Docker login') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'ecr-create'
                ]]) {
                    sh 'aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_REGISTRY'
                }
            }
        }

        stage('Build image') {
            steps {
                dir('cattlepoint-aer3-week2') {
                    sh 'docker build -t $ECR_URI:latest $DOCKER_CONTEXT_DIR/.'
                }
            }
        }

        stage('Push image') {
            steps {
                sh 'docker push $ECR_URI:latest'
            }
        }
    }
}

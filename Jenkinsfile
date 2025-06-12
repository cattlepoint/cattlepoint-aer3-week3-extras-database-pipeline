// Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Clone repo') {
            steps {
                sh 'git clone cattlepoint/cattlepoint-aer3-week2'
            }
        }
        stage('Docker login') {
            steps {
                sh '''
                    docker login -u AWS -p $(aws ecr get-login-password) \
                    $(aws ecr describe-repositories --repository-names cattlepoint-database \
                          --query "repositories[0].repositoryUri" --output text)
                '''
            }
        }
        stage('Build image') {
            steps {
                sh '''
                    docker build -t $(aws ecr describe-repositories --repository-names cattlepoint-database \
                                         --query "repositories[0].repositoryUri" --output text):latest \
                               database/.
                '''
            }
        }
        stage('Push image') {
            steps {
                sh '''
                    docker push $(aws ecr describe-repositories --repository-names cattlepoint-database \
                                      --query "repositories[0].repositoryUri" --output text):latest
                '''
            }
        }
    }
}

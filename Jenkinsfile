pipeline {
    agent any

    stages {
        stage('Checkout Source') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khasnhnq/devops-test-khanh.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Project') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker build -t devops-test-khanh .

                    docker rm -f devops-test-khanh || true

                    docker run -d \
                      --name devops-test-khanh \
                      -p 3001:80 \
                      devops-test-khanh
                '''
            }
        }
    }

    post {
        success {
            echo 'DEPLOY SUCCESS'
        }

        failure {
            echo 'DEPLOY FAILED'
        }
    }
}
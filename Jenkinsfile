pipeline {
    agent any

    environment {
        PROJECT_NAME = 'devops-test-khanh'
        DEPLOY_URL = 'http://localhost:3001'
    }

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
                withCredentials([
                    string(credentialsId: 'telegram-bot-token', variable: 'BOT_TOKEN'),
                    string(credentialsId: 'telegram-chat-id', variable: 'CHAT_ID')
                ]) {
                    sh '''
                        curl -s -X POST \
                        "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
                        -d chat_id="${CHAT_ID}" \
                        --data-urlencode "text=DEPLOY STARTED
Project: ${PROJECT_NAME}
Branch: main" || true
                    '''
                }

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
            withCredentials([
                string(credentialsId: 'telegram-bot-token', variable: 'BOT_TOKEN'),
                string(credentialsId: 'telegram-chat-id', variable: 'CHAT_ID')
            ]) {
                sh '''
                    curl -s -X POST \
                    "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
                    -d chat_id="${CHAT_ID}" \
                    --data-urlencode "text=DEPLOY SUCCESS
Project: ${PROJECT_NAME}
Branch: main
URL: ${DEPLOY_URL}" || true
                '''
            }
        }

        failure {
            withCredentials([
                string(credentialsId: 'telegram-bot-token', variable: 'BOT_TOKEN'),
                string(credentialsId: 'telegram-chat-id', variable: 'CHAT_ID')
            ]) {
                sh '''
                    curl -s -X POST \
                    "https://api.telegram.org/bot${BOT_TOKEN}/sendMessage" \
                    -d chat_id="${CHAT_ID}" \
                    --data-urlencode "text=DEPLOY FAILED
Project: ${PROJECT_NAME}
Branch: main
Please check Jenkins." || true
                '''
            }
        }
    }
}
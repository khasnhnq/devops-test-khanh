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

                // Telegram: Deploy Started
                withCredentials([
                    string(
                        credentialsId: 'telegram-bot-token',
                        variable: 'BOT_TOKEN'
                    ),
                    string(
                        credentialsId: 'telegram-chat-id',
                        variable: 'CHAT_ID'
                    )
                ]) {
                    sh '''
                        curl -s -X POST \
                          "https://api.telegram.org/bot${8823348287:AAFR-ejt22LfZEmzKE6NhcP85APq4e6hCbg}/sendMessage" \
                          -d chat_id="${7133160006}" \
                          --data-urlencode "text=DEPLOY STARTED
Project: ${PROJECT_NAME}
Branch: main" || true
                    '''
                }

                // Build Docker + Deploy
                sh '''
                    echo "===== BUILD DOCKER IMAGE ====="

                    docker build -t devops-test-khanh .

                    echo "===== REMOVE OLD CONTAINER ====="

                    docker rm -f devops-test-khanh || true

                    echo "===== DEPLOY ====="

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

            withCredentials([
                string(
                    credentialsId: 'telegram-bot-token',
                    variable: 'BOT_TOKEN'
                ),
                string(
                    credentialsId: 'telegram-chat-id',
                    variable: 'CHAT_ID'
                )
            ]) {
                sh '''
                    curl -s -X POST \
                      "https://api.telegram.org/bot${8823348287:AAFR-ejt22LfZEmzKE6NhcP85APq4e6hCbg}/sendMessage" \
                      -d chat_id="${7133160006}" \
                      --data-urlencode "tex
                      t=DEPLOY SUCCESS
Project: ${PROJECT_NAME}
Branch: main
URL: ${DEPLOY_URL}" || true
                '''
            }
        }

        failure {

            echo 'DEPLOY FAILED'

            withCredentials([
                string(
                    credentialsId: 'telegram-bot-token',
                    variable: 'BOT_TOKEN'
                ),
                string(
                    credentialsId: 'telegram-chat-id',
                    variable: 'CHAT_ID'
                )
            ]) {
                sh '''
                    curl -s -X POST \
                      "https://api.telegram.org/bot${8823348287:AAFR-ejt22LfZEmzKE6NhcP85APq4e6hCbg}/sendMessage" \
                      -d chat_id="${7133160006}" \
                      --data-urlencode "text=DEPLOY FAILED
Project: ${PROJECT_NAME}
Branch: main
Please check Jenkins." || true
                '''
            }
        }
    }
}
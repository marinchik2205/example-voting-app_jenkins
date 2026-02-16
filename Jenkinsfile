pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out source code...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building Docker images...'
                sh '''
                    docker build -t voting-app-vote ./vote
                    docker build -t voting-app-result ./result
                    docker build -t voting-app-worker ./worker
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                echo '🧪 Running tests (placeholder)...'
                sh '''
                    echo "No unit tests configured yet"
                '''
            }
        }

        stage('Package') {
            steps {
                echo '📦 Verifying built images...'
                sh '''
                    docker images | grep voting-app
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Task #13 Complete!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}

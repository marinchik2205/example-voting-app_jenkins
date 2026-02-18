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

        stage('Static Code Checks') {
            steps {
                echo '🔍 Running static code analysis...'
                sh '''
                    chmod +x ./run-static-checks.sh
                    ./run-static-checks.sh || true
                '''
            }
        }

        stage('Security Scan') {
            steps {
                echo '🔒 Running Trivy vulnerability scan...'
                sh '''
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                      aquasec/trivy image --severity HIGH,CRITICAL --exit-code 1 \
                      voting-app-vote:latest || echo "Vulnerabilities found in vote service"
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                      aquasec/trivy image --severity HIGH,CRITICAL --exit-code 1 \
                      voting-app-result:latest || echo "Vulnerabilities found in result service"
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                      aquasec/trivy image --severity HIGH,CRITICAL --exit-code 1 \
                      voting-app-worker:latest || echo "Vulnerabilities found in worker service"
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                echo '🧪 Running tests...'
                sh '''
                    chmod +x ./result/tests/tests.sh
                    ./result/tests/tests.sh || true
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
        always {
            echo '📊 Archiving test reports and build artifacts...'
            archiveArtifacts artifacts: 'result/tests/test-report.txt', allowEmptyArchive: true
            archiveArtifacts artifacts: 'worker/bin/**/*.dll,worker/bin/**/*.exe', allowEmptyArchive: true
            archiveArtifacts artifacts: 'result/dist/**/*', allowEmptyArchive: true
            archiveArtifacts artifacts: 'vote/build/**/*', allowEmptyArchive: true
            
            junit testResults: 'result/tests/test-report.txt', allowEmptyResults: true
        }
        success {
            echo '✅ Build successful!'
        }
        failure {
            echo '❌ Build failed!'
        }
    }
}

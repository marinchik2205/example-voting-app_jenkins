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
                    docker build -t voting-app-vote:latest ./vote
                    docker build -t voting-app-result:latest ./result
                    docker build -t voting-app-worker:latest ./worker
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
                    mkdir -p reports

                    docker run --rm \
                      -v /var/run/docker.sock:/var/run/docker.sock \
                      -v $(pwd)/reports:/reports \
                      aquasec/trivy image \
                      --severity HIGH,CRITICAL \
                      --format json \
                      -o /reports/vote-scan-report.json \
                      voting-app-vote:latest || true

                    docker run --rm \
                      -v /var/run/docker.sock:/var/run/docker.sock \
                      -v $(pwd)/reports:/reports \
                      aquasec/trivy image \
                      --severity HIGH,CRITICAL \
                      --format json \
                      -o /reports/result-scan-report.json \
                      voting-app-result:latest || true

                    docker run --rm \
                      -v /var/run/docker.sock:/var/run/docker.sock \
                      -v $(pwd)/reports:/reports \
                      aquasec/trivy image \
                      --severity HIGH,CRITICAL \
                      --format json \
                      -o /reports/worker-scan-report.json \
                      voting-app-worker:latest || true
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
            echo '📊 Archiving scan reports and build artifacts...'

            archiveArtifacts artifacts: 'reports/**/*.*', allowEmptyArchive: true, fingerprint: true
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


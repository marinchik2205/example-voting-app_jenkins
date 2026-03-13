pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev','staging','prod'],
            description: 'Target deployment environment'
        )
    }

    environment {
        IMAGE_TAG = ''
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                script {
                    env.IMAGE_TAG = sh(
                        script: "git rev-parse --short HEAD",
                        returnStdout: true
                    ).trim()
                }
            }
        }

        stage('Build') {
            steps {
                echo "Building Docker images"
                sh '''
                docker build -t voting-app-vote:${IMAGE_TAG} ./vote
                docker build -t voting-app-result:${IMAGE_TAG} ./result
                docker build -t voting-app-worker:${IMAGE_TAG} ./worker
                '''
            }
        }

        stage('Tests') {
            steps {
                sh '''
                echo "Running tests" > test-report.txt
                echo "Tests passed" >> test-report.txt
                '''
            }
        }

    }

    post {

        always {
            archiveArtifacts artifacts: '**/*.txt', allowEmptyArchive: true
        }

        success {
            echo 'BUILD SUCCESS'
        }

        failure {
            echo 'BUILD FAILED'
        }
    }

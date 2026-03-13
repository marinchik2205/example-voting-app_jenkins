pipeline {
    agent any

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev','staging','prod'],
            description: 'Target deployment environment'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building application"
            }
        }

        stage('Test') {
            steps {
                echo "Running tests"
            }
        }

    }

    post {
        success {
            echo "BUILD SUCCESS"
        }
        failure {
            echo "BUILD FAILED"
        }
    }
}

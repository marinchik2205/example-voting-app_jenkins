pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building project"
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

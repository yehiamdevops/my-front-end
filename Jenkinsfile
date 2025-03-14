pipeline {
    agent any // Run on any available agent

    environment {
        // Define environment variables
        APP_NAME = "my-app"
        BUILD_NUMBER = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from SCM (e.g., Git)
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Build the application
                sh 'echo "Building ${APP_NAME}..."'
                sh 'make build' // Replace with your build command
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh 'echo "Running tests..."'
                sh 'make test' // Replace with your test command
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application
                sh 'echo "Deploying ${APP_NAME}..."'
                sh 'make deploy' // Replace with your deploy command
            }
        }
    }

    post {
        success {
            // Actions to perform if the pipeline succeeds
            sh 'echo "Pipeline succeeded!"'
        }
        failure {
            // Actions to perform if the pipeline fails
            sh 'echo "Pipeline failed!"'
        }
    }
}
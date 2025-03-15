pipeline {
    agent {
        label 'win'
    }

    environment {
        // Define environment variables (can be loaded from Jenkins credentials)
        EXPRESS_HOST = credentials('EXPRESS_HOST') // Fetch from Jenkins credentials
        SOCKET_HOST = credentials('SOCKET_HOST')
        SOCKET_PORT = credentials('SOCKET_PORT')
        CLOUDINARY_URL = credentials('CLOUDINARY_URL')
        GITHUB_TOKEN = credentials('github-token')
    }

    stages {

        // Stage 1: Checkout code from SCM (Git)
        stage('Checkout') {
            steps {
                checkout scm // Checkout code from the configured SCM (e.g., Git)
            
            }
                    
        }

        stage('Add Env Vars'){
            steps{
             script {
                    
                    bat """
                        setx EXPRESS_HOST "${env.EXPRESS_HOST}"
                        setx SOCKET_HOST "${env.SOCKET_HOST}"
                        setx SOCKET_PORT "${env.SOCKET_PORT}"
                        setx CLOUDINARY_URL "${env.CLOUDINARY_URL}"
                        
                        """
                    }
                
            }

        }

    }

    post {
        // Actions to perform after the pipeline completes
        success {
            echo 'Pipeline succeeded! 🎉'
            // Notify the team (e.g., via email, Slack, etc.)
        }
        failure {
            echo 'Pipeline failed! 😢'
            // Notify the team (e.g., via email, Slack, etc.)
        }
    }
}
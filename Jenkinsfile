pipeline {
    agent any // Run on any available agent

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

        // Stage 2: Build the application using Gradle
        stage('Build') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew clean build' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew clean build' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 3: Create EXE file
        stage('Create EXE') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew createExe' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew createExe' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 4: Create ZIP file
        stage('Create Zip') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew zipLaunch4j' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew zipLaunch4j' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 5: Publish GitHub Release
        stage('Publish GitHub Release') {
            steps {
                script {
                    // Use the GitHub Release Plugin or a script to upload the ZIP file
                    def releaseId = githubRelease(
                        repoOwner: 'yehiamdevops', // Your GitHub username or organization
                        repository: 'my-front-end', // Your repository name
                        tagName: 'v1.0.0', // Release tag (e.g., v1.0.0)
                        releaseName: 'Release v1.0.0', // Release name
                        releaseBody: 'This is the first release!', // Release description
                        assets: 'app/build/distributions/*.zip', // Path to the ZIP file
                        credentialsId: 'github-token' // Jenkins credentials for GitHub token
                    )
                    echo "GitHub release created with ID: ${releaseId}"
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
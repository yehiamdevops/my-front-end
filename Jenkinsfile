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
                 script {
                    if (isUnix()) {
                        sh 'chmod +x gradlew' // Make gradlew executable on Unix-like systems
                    }
                }
            }
            
        }

        // Stage 2: Build the application using Gradle
        stage('Build') {
            steps {
                script {
                    if (isUnix()) {
                        sh './gradlew clean build' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew.bat clean build' // Use 'bat' for Windows
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
                        bat 'gradlew.bat createExe' // Use 'bat' for Windows
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
                        bat 'gradlew.bat zipLaunch4j' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 5: Publish GitHub Release
        stage('Publish GitHub Release') {
            steps {
                script {
                    def zipFile = 'app/build/distributions/forrealdatingapp.zip'
                    def tagName = "v${env.BUILD_NUMBER}" // Use build number for versioning
                    def releaseName = "Release ${tagName}"
                    def releaseBody = "This is release ${tagName} built by Jenkins."

                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        githubRelease(
                            credentialsId: 'github-token',
                            gitHubServer: 'https://api.github.com',
                            repositoryOwner: 'yehiamdevops',
                            repositoryName: 'my-front-end',
                            tagName: tagName,
                            releaseName: releaseName,
                            releaseBody: releaseBody,
                            assets: zipFile
                        )
                    }
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
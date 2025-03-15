pipeline {
    agent {
        node 'HostAgent'
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
        stage('Add Env Vars'){
            steps{
             script {
                if (!isUnix()) {
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
                        sh './gradlew createExe copyJavafxSdk' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew.bat createExe copyJavafxSdk' // Use 'bat' for Windows
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
            // Create release notes file
            writeFile file: 'release-notes.md', text: "Release ${env.BUILD_NUMBER} - Built by Jenkins"

            // Create GitHub release
            createGitHubRelease(
                credentialId: 'github-token', // Jenkins credential ID for GitHub PAT
                repository: 'yehiamdevops/my-front-end', // Format: owner/repo
                tag: "v${env.BUILD_NUMBER}", // Release tag
                commitish: 'main', // Branch/commit reference (required)
                bodyFile: 'release-notes.md', // Release description file
                draft: false // Publish immediately
            )

          
            // Upload ZIP asset
            uploadGithubReleaseAsset(
                credentialId: 'github-token',
                repository: 'yehiamdevops/my-front-end',
                tagName: "v${env.BUILD_NUMBER}",
                uploadAssets: [
                    [filePath: "C:\\Users\\yehiam\\workspace\\my-front-end\\app\\build\\distributions\\forrealdatingapp.zip"]
                ]
            )
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
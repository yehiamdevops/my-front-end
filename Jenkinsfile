pipeline {
    agent none

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
            agent any
            steps {
                checkout scm // Checkout code from the configured SCM (e.g., Git)
             
            }
            
        }
        stage('Add Env Vars'){
            agent {
                label 'win' // Run on a Windows agent
            }
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
        //  Build the application using Gradle
        stage('Build') {
            agent any
            steps {
                script {
                    if (isUnix()) {
                        sh 'chmod +x gradlew'
                        sh './gradlew clean build' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew.bat clean build' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 3: Create EXE file
        stage('Create EXE') {
            agent any
            steps {
                script {
                    if (isUnix()) {
                        sh 'chmod +x gradlew'
                        sh './gradlew createExe copyJavafxSdk' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew.bat createExe copyJavafxSdk' // Use 'bat' for Windows
                    }
                }
            }
        }

        // Stage 4: Create ZIP file
        stage('Create Zip') {
            agent any
            steps {
                script {
                    if (isUnix()) {
                        sh 'chmod +x gradlew'
                        sh './gradlew zipLaunch4j' // Use 'sh' for Linux/macOS
                    } else {
                        bat 'gradlew.bat zipLaunch4j' // Use 'bat' for Windows
                    }
                }
            }
        }

    

        

        // Stage 5: Publish GitHub Release
        stage('Publish GitHub Release') {
            agent {
                label 'unix' // Run on a Windows agent
            }
            steps {
                script {
                    // Reuse the same ZIP file path 
                    
                    def zipFilePath = "${WORKSPACE}/app/build/distributions/forrealdatingapp.zip"
                        

                    // Log the file path being used
                    echo "Attempting to upload file: ${zipFilePath}"


                    // Create release notes file
                    writeFile file: 'release-notes.md', text: "Release ${env.BUILD_NUMBER} - Built by Jenkins"
                    // Create GitHub release
                    createGitHubRelease(
                        credentialId: 'github-token',
                        repository: 'yehiamdevops/my-front-end',
                        tag: "v${env.BUILD_NUMBER}",
                        commitish: 'main',
                        bodyFile: 'release-notes.md',
                        draft: false
                    )

                    // Upload ZIP asset
                    uploadGithubReleaseAsset(
                        credentialId: 'github-token',
                        repository: 'yehiamdevops/my-front-end',
                        tagName: "v${env.BUILD_NUMBER}",
                        uploadAssets: [
                            [filePath: zipFilePath]
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
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
            // Define variables
            def zipFile = 'app/build/distributions/forrealdatingapp.zip' // Path to the ZIP file
            def tagName = 'v1.0.0' // Release tag
            def releaseName = 'Release v1.0.0' // Release name
            def releaseBody = 'This is the first release!' // Release description

            // Step 1: Create a GitHub release
            def releaseResponse = sh(script: """
                curl -X POST \
                -H "Authorization: token ${GITHUB_TOKEN}" \
                -H "Content-Type: application/json" \
                -d '{
                    "tag_name": "${tagName}",
                    "name": "${releaseName}",
                    "body": "${releaseBody}",
                    "draft": false,
                    "prerelease": false
                }' \
                https://api.github.com/repos/yehiamdevops/my-front-end/releases
            """, returnStdout: true)

            // Step 2: Parse the release ID from the response
            def releaseId = (new groovy.json.JsonSlurper().parseText(releaseResponse)).id

            // Step 3: Upload the ZIP file to the release
            sh """
                curl -X POST \
                -H "Authorization: token ${GITHUB_TOKEN}" \
                -H "Content-Type: application/zip" \
                --data-binary @${zipFile} \
                https://uploads.github.com/repos/yehiamdevops/my-front-end/releases/${releaseId}/assets?name=${zipFile}
            """

            // Print the release ID for debugging
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
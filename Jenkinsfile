pipeline {
    agent any
    // agent {
    //     docker {
    //         image 'node:20-alpine'
    //         args '-u root' // Use root if needed for permissions inside the container
    //     }
    // }

    environment {
        // Replace with your actual Nexus URL and repository name
        NEXUS_URL = 'localhost:8081'
        NEXUS_REPOSITORY_ID = 'backend-1'
        NEXUS_GROUP_ID = 'com.expense'
        def NEXUS_VERSION = ''
        NEXUS_ARTIFACT_ID = 'backend'
        def ARTIFACT_FILE_NAME = ''
        
        // This is the ID of your Jenkins Credentials (Username with password)
        NEXUS_CREDENTIALS_ID = 'nexus-auth'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    stages {
        stage('Checkout') {
            steps {
                // The pipeline setup via the UI takes care of the Git checkout
                echo "Source code checked out successfully."
            }
        }

        stage('Read the Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    def appVersion = packageJson.version
                    echo "Application version: ${appVersion}"
                    
                    NEXUS_VERSION = appVersion
                    ARTIFACT_FILE_NAME = "${env.NEXUS_ARTIFACT_ID}-${NEXUS_VERSION}.zip"
                    
                    echo "Application version: ${env.NEXUS_VERSION}"
                    echo "Artifact file name: ${env.ARTIFACT_FILE_NAME}"
                }
            }
        }

        stage('Testing') {
            steps {
                sh "echo ${env.NEXUS_VERSION}"
                sh "echo ${env.ARTIFACT_FILE_NAME}"
                // Add your test commands here, e.g., sh 'npm test'
            }
        }

        // stage('Push Artifacts') {
        //     steps {
        //         script {
        //             nexusArtifactUploader(
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'http',
        //                 nexusUrl: NEXUS_URL,
        //                 groupId: NEXUS_GROUP_ID,
        //                 version: NEXUS_VERSION,
        //                 repository: NEXUS_REPOSITORY_ID,
        //                 credentialsId: NEXUS_CREDENTIALS_ID,
        //                 artifacts: [
        //                     [
        //                         artifactId: NEXUS_ARTIFACT_ID,
        //                         classifier: '',
        //                         file: ARTIFACT_FILE_NAME,
        //                         type: 'zip'
        //                     ]
        //                 ]
        //             )
        //         }
        //     }
        // }
    }

    post {
        always {
            echo 'This will run always'
            deleteDir()
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
    }
}
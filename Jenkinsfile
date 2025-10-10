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
        // NEXUS_VERSION = ''
        NEXUS_ARTIFACT_ID = 'backend'
        // ARTIFACT_FILE_NAME = ''
        
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
                    
                    env.NEXUS_VERSION = appVersion
                    env.ARTIFACT_FILE_NAME = "${env.NEXUS_ARTIFACT_ID}-${env.NEXUS_VERSION}.zip"
                    
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
        stage('Install Dependencies') {
            steps {
                sh """
                ls -ltr
                npm install
                """
            }
        }
        stage('App Packaging') {
            steps {
                sh """
                zip -r -q ${env.ARTIFACT_FILE_NAME} * -x Jenkinsfile* -x *.zip -x *.git*
                """
            }
        }

        stage('Push Artifacts') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: env.NEXUS_URL,
                        groupId: env.NEXUS_GROUP_ID,
                        version: env.NEXUS_VERSION,
                        repository: env.NEXUS_REPOSITORY_ID,
                        credentialsId: env.NEXUS_CREDENTIALS_ID,
                        artifacts: [
                            [
                                artifactId: env.NEXUS_ARTIFACT_ID,
                                classifier: '',
                                file: env.ARTIFACT_FILE_NAME,
                                type: 'zip'
                            ]
                        ]
                    )
                }
            }
        }
        stage('Deploy Application') {
            steps {
                script {
                    def DEPLOY_DIR = "/home/yaswanth/expense-app"
                    def DOWNLOAD_URL = "http://${env.NEXUS_URL}/repository/${env.NEXUS_REPOSITORY_ID}/${env.NEXUS_GROUP_ID.replace('.', '/')}/${env.NEXUS_ARTIFACT_ID}/${env.NEXUS_VERSION}/${env.ARTIFACT_FILE_NAME}"

                    echo "Deploying from Nexus: ${DOWNLOAD_URL}"
                     withCredentials([usernamePassword(credentialsId: env.NEXUS_CREDENTIALS_ID, 
                                                      usernameVariable: 'NEXUS_USER', 
                                                      passwordVariable: 'NEXUS_PASS')]){
                    sh """
                    mkdir -p ${DEPLOY_DIR}
                    cd ${DEPLOY_DIR}
                    
                    echo "Downloading artifact..."
                    curl -u ${NEXUS_USER}:${NEXUS_PASS}  -O ${DOWNLOAD_URL}

                    echo "Unzipping..."
                    unzip -o ${env.ARTIFACT_FILE_NAME}

                    echo "Starting/Restarting app using PM2..."
                    pm2 describe expense-app > /dev/null 2>&1
                    if [ \$? -eq 0 ]; then
                        echo "App already running — restarting..."
                        pm2 restart expense-app
                    else
                        echo "Starting app for the first time..."
                        pm2 start index.js --name expense-app
                    fi

                    pm2 save
                    """
                                                      }
                }
            }
        }
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
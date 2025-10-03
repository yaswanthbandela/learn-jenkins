pipeline {
        agent {
        docker {
            image 'node:20-alpine'
            args '-u root' // Use root if needed for permissions inside the container
        }
    }
    
    environment {
        // Replace with your actual Nexus URL and repository name
        NEXUS_URL = 'http://localhost:8081/repository/backend-1/' 
        // These should be configured as Jenkins Credentials (Secret Text)
        NEXUS_USERNAME = credentials('nexus-username-id') 
        NEXUS_PASSWORD = credentials('nexus-password-id') 
        
        // Assuming your artifact is a simple tarball of the built app
        ARTIFACT_NAME = "my-nodejs-app-${env.BUILD_NUMBER}.tgz" 
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
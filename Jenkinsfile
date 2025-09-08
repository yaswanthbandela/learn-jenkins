pipeline {
    agent any
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Hello, This is Build stage"
                echo Testing github webhook integration
                echo Testing github webhook integration 2
                echo Testing github webhook integration 3
                echo Testing github webhook integration 4
                '''
            }
        }
        stage('Test') {
             steps {
                sh 'echo "Hello, This is Test stage"'
            }            
        }
        stage('Deploy') {
             steps {
                sh 'echo "Hello, This is Deploy stage"'
            }            
        }

    }
}
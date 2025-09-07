pipeline {
    agent any
    options {
        timeout(time: 1, unit: 'SECONDS') 
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Hello, This is Build stage"
                sleep 10
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
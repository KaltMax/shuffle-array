pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo "Hello Build"
            }
        }
        
        stage('Test') {
            steps {
                echo "Hello Test"
            }
        }
        
        stage('Check Version & Publish') {
            steps {
                echo "Hello Publish"
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
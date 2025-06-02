pipeline {
    agent {
        docker {
            image 'node:22.14.0-alpine'
        }
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm run test'
            }
        }
        
        stage('Check Version & Publish') {
            steps {
                script {
                    // Check if version has changed compared to previous commit
                    def currentVersion = sh(script: "node -e 'console.log(require(\"./package.json\").version)'", returnStdout: true).trim()
                    echo "Current version is: ${currentVersion}"
                    
                    def previousVersion = ""
                    try {
                        previousVersion = sh(script: "git show HEAD^:package.json | grep version | head -1 | awk -F: '{ print \$2 }' | sed 's/[\", ]//g'", returnStdout: true).trim()
                        echo "Previous version was: ${previousVersion}"
                    } catch (Exception e) {
                        echo "Could not get previous version, assuming first commit"
                    }
                    
                    if (currentVersion != previousVersion) {
                        echo "Version changed. Publishing to npm..."
                        withCredentials([string(credentialsId: 'npm-token', variable: 'NPM_TOKEN')]) {
                            sh 'echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" > .npmrc'
                            sh 'npm publish --access public'
                        }
                    } else {
                        echo "Version unchanged. Skipping publish."
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
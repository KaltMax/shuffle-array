pipeline {
    agent any

    tools {
        nodejs 'NodeJS 18' // Use the name you configured in the Jenkins UI
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
                    def currentVersion = sh(script: "node -e \"console.log(require('./package.json').version)\"", returnStdout: true).trim()
                    echo "Current version is: ${currentVersion}"

                    def previousVersion = ""
                    try {
                        previousVersion = sh(script: "git show HEAD^:package.json | grep version | head -1 | awk -F: '{ print \$2 }' | sed 's/[\", ]//g'", returnStdout: true).trim()
                        echo "Previous version was: ${previousVersion}"
                    } catch (Exception e) {
                        echo "Could not get previous version, assuming first commit"
                    }

                    if (currentVersion != previousVersion) {
                        echo "Version changed, publishing..."
                        withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
                            sh 'echo "//registry.npmjs.org/:_authToken=${NPM_TOKEN}" > .npmrc'
                            sh 'npm publish --access public'
                            sh 'rm -f .npmrc'
                        }
                    } else {
                        echo "Version unchanged, skipping publish"
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
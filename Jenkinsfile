pipeline {
    agent any

    environment {
        APP_NAME = ''
        APP_VERSION = ''
        NEW_VERSION = '1.0.1'   // change version here
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Read & Update package.json') {
            steps {
                script {
                    // Read package.json
                    def packageJson = readJSON file: 'package.json'

                    // Get current values
                    env.APP_NAME = packageJson.name
                    env.APP_VERSION = packageJson.version

                    echo "Current Version: ${env.APP_VERSION}"

                    // Update version
                    packageJson.version = env.NEW_VERSION

                    // Write back to file
                    writeJSON file: 'package.json', json: packageJson, pretty: 4

                    echo "Updated Version: ${env.NEW_VERSION}"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                script {
                    sh '''
                        npm install '''
                }
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Running tests..."
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Deploying $APP_NAME version $NEW_VERSION"
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline SUCCESS for ${env.APP_NAME}:${env.NEW_VERSION}"
        }
        failure {
            echo "Pipeline FAILED"
        }
    }
}
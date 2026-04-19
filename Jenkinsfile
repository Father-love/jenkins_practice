pipeline {
    agent any

    environment {
        APP_NAME = ''
        APP_VERSION = ''
        NEW_VERSION = '1.0.1'
        REGION = "ap-southeast-2"
        ACC_ID = "154586927356"   // ✅ fixed (12 digits)
        REPO = "father-love-jenkins-practice"  // ✅ valid ECR repo
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
                    def packageJson = readJSON file: 'package.json'

                    env.APP_NAME = packageJson.name
                    env.APP_VERSION = packageJson.version

                    echo "Current Version: ${env.APP_VERSION}"

                    packageJson.version = env.NEW_VERSION

                    writeJSON file: 'package.json', json: packageJson, pretty: 4

                    echo "Updated Version: ${env.NEW_VERSION}"
                }
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Docker Build & Push') {
            steps {
                withAWS(region: "${REGION}", credentials: 'aws-auth') {
                    sh """
                    aws ecr get-login-password --region ${REGION} | \
                    docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.${REGION}.amazonaws.com

                    docker build -t ${REPO}:${NEW_VERSION} .

                    docker tag ${REPO}:${NEW_VERSION} \
                    ${ACC_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPO}:${NEW_VERSION}

                    docker push \
                    ${ACC_ID}.dkr.ecr.${REGION}.amazonaws.com/${REPO}:${NEW_VERSION}
                    """
                }
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
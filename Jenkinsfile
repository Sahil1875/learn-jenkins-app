pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '70fbb273-a271-4e8e-8d43-4cd19534139f'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {

        stage('Install dependencies') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.48.0-jammy'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  npm ci
                  npm install netlify-cli --no-save
                '''
            }
        }

        stage('Unit Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.48.0-jammy'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  npm test
                '''
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.48.0-jammy'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  npm run build
                  ls -la build
                '''
            }
        }

        stage('Playwright E2E Tests') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.48.0-jammy'
                    args '-u root'
                }
            }
            steps {
                sh '''
                  npm install -g serve
                  serve -s build -l 3000 &
                  sleep 5
                  npx playwright install
                  npx playwright test
                '''
            }
        }

        stage('Deploy Staging') {
            steps {
                sh '''
                  npx netlify deploy --dir=build
                '''
            }
        }

        stage('Approval') {
            steps {
                input message: 'Ready to Deploy?', ok: 'Yes, deploy to PROD'
            }
        }

        stage('Deploy Prod') {
            steps {
                sh '''
                  npx netlify deploy --dir=build --prod
                '''
            }
        }
    }

    post {
        always {
            junit allowEmptyResults: true, testResults: 'jest-results/junit.xml'
        }
        success {
            echo "Build completed successfully 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

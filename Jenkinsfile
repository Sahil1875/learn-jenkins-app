pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.48.0-jammy'
            args '-u root'
            reuseNode true
        }
    }

    environment {
        NETLIFY_SITE_ID = '70fbb273-a271-4e8e-8d43-4cd19534139f'
        NETLIFY_AUTH_TOKEN = credentials('netlify_token')
    }

    stages {

        stage('Install dependencies') {
            steps {
                sh '''
                  npm ci
                  npm install netlify-cli --no-save
                '''
            }
        }

        stage('Unit Tests') {
            steps {
                sh '''
                  npm test
                  test -f build/index.html
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                  npm run build
                  ls -la build
                '''
            }
        }

        stage('Playwright E2E Tests') {
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
                  echo "Deploying to staging..."
                  npx netlify status
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
                  echo "Deploying to production..."
                  npx netlify deploy --dir=build --prod
                '''
            }
        }
    }

    post {
        success {
            echo "Build completed successfully 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

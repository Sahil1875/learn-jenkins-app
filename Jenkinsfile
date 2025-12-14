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
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    
    stages {

        stage('Install dependencies') {
            steps {
                sh 'ls -la'
                sh 'npm ci'
            }
        }
        
        stage('Unit Tests') {
            steps{
                sh '''
                npm test
                test -f build/index.html
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
                sh 'ls -la build'
            }
        }

        stage('Playwright E2E Tests') {
            steps {
                sh '''
                  # Install serve to host built React app
                  npm install -g serve

                  # Start the UI in background on port 3000
                  serve -s build -l 3000 &

                  # Wait 5 seconds for server to start
                  sleep 5

                  npx playwright install

                  # Run Playwright test suite
                  npx playwright test
                '''
            }
        }

        stage('Deploy Staging'){
            steps{
                sh '''
                npm install netlify-cli -g
                netlify --version
                echo " Deploying to production site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build 
                '''
            }
        }

        stage('Approval'){
            steps{
                input message: 'Ready to Deploy?', ok: 'Yes, I am sure to deploy'
            }
        }
        
        stage('Deploy Prod'){
            steps{
                sh '''
                npm install netlify-cli -g
                netlify --version
                echo " Deploying to production site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --prod
                '''
            }
        }
    }

    post {
        always{
             junit 'jest-results/junit.xml'
             archiveArtifacts artifacts: 'build/**', fingerprint: true
        }
        success {
            echo "Build completed successfully! 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

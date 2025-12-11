pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.48.0-jammy' 
            args '-u root'
            reuseNode true
        }
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

                  # Install Playwright browsers if needed
                  npx playwright install

                  # Run Playwright test suite
                  npx playwright test
                '''
            }
        }
    }

    post {
        always{
             junit 'test-results/jest/*.xml'
             junit 'test-results/playwright/*.xml'
        }
        success {
            echo "Build completed successfully! 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

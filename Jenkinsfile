pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.39.0-jammy ' 
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

                  # Run Playwright test suite
                  npx playwright test
                '''
            }
        }

        stage('Deploy'){
            steps{
                sh '''
                npm install netlify-cli -g
                netlify --version
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

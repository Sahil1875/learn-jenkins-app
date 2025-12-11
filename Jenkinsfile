pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-u root'    // prevents permission issues inside container
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
        
        stage('Test'){
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
                sh 'ls -la'
            }
        }
    }

    post {
        always{
            junit 'test-results/junit.xml'
        }
        success {
            echo "Build completed successfully! 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

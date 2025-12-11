pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-u root'    // prevents permission issues inside container
            reuseNode true
        }
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'ls -la'
                sh 'npm ci'
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
        success {
            echo "Build completed successfully! 🚀"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}

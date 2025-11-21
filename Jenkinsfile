pipeline {
    agent {
        docker {
            image 'node:20'
            args '-u root:root'
        }
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Check Environment') {
            steps {
                sh '''
                    node --version
                    npm --version
                    pwd
                    ls -la
                '''
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'npm run lint || true'
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm run test:unit || true'
            }
        }
        
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'release/**,dist/**', allowEmptyArchive: true
            }
        }
    }
    
    post {
        success {
            echo '✅ Білд успішний!'
        }
        failure {
            echo '❌ Білд провалився'
        }
    }
}

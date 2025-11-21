pipeline {
    agent {
        docker {
            image 'node:22'
            args '-u root:root'
        }
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Initialize Submodules') {
            steps {
                sh '''
                    git submodule update --init --recursive
                '''
            }
        }
        
        stage('Check Environment') {
            steps {
                sh '''
                    node --version
                    npm --version
                    git --version
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
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'release/**,dist/**', allowEmptyArchive: true
            }
        }
    }
    
    post {
        success {
            echo '✅ Build успішний!'
        }
        failure {
            echo '❌ Build провалився'
        }
        always {
            cleanWs()
        }
    }
}

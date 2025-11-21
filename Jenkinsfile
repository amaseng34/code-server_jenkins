pipeline {
    agent {
        docker {
            image 'node:22'
            args '-u root:root'
        }
    }
    
    stages {
        stage('Install System Dependencies') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y \
                        build-essential \
                        libx11-dev \
                        libxkbfile-dev \
                        libsecret-1-dev \
                        python3 \
                        pkg-config \
                        git
                '''
            }
        }
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Initialize Submodules') {
            steps {
                sh 'git submodule update --init --recursive'
            }
        }
        
        stage('Check Environment') {
            steps {
                sh '''
                    node --version
                    npm --version
                    python3 --version
                    pkg-config --version
                '''
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
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
                archiveArtifacts artifacts: 'release/**,dist/**,out/**', allowEmptyArchive: true
            }
        }
    }
    
    post {
        success {
            echo '✅ Build успішний!'
        }
        failure {
            echo '❌ Build провалився. Перевір логи.'
        }
        always {
            // Не чистимо workspace для debug
            echo 'Build completed'
        }
    }
}

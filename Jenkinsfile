pipeline {
    agent any
    
    environment {
        NODE_VERSION = '20'
        DOCKER_IMAGE = 'code-server-test'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Node.js') {
            steps {
                script {
                    // Використовуємо NodeJS plugin або встановлюємо через nvm
                    sh '''
                        curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
                        export NVM_DIR="$HOME/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                        nvm install ${NODE_VERSION}
                        nvm use ${NODE_VERSION}
                        node --version
                        npm --version
                    '''
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    npm ci
                '''
            }
        }
        
        stage('Lint') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    npm run lint || true
                '''
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    npm run build
                '''
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    npm run test:unit || true
                '''
            }
        }
        
        stage('Package') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use ${NODE_VERSION}
                    npm run release || npm run package || echo "No package script"
                '''
            }
        }
        
        stage('Build Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    sh '''
                        docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                        docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'release/**,dist/**', allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Build успішний! 🎉'
        }
        failure {
            echo 'Build провалився 😢'
        }
    }
}

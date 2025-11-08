pipeline {
    agent any
    
    environment {
        BACKEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-backend'
        FRONTEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-frontend'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'docker build -t ${BACKEND_IMAGE_NAME}:${VERSION} .'
                }
            }
        }
        
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t ${FRONTEND_IMAGE_NAME}:${VERSION} .'
                }
            }
        }
        
        stage('Verify Images') {
            steps {
                sh """
                    echo "Imagenes construidas:"
                    docker images | grep sofiac14
                """
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline ejecutado exitosamente'
        }
        failure {
            echo 'Pipeline fallo'
        }
    }
}

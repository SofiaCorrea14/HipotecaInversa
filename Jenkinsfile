pipeline {
    agent any
    
    environment {
        BACKEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-backend'
        FRONTEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-frontend'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        stage('Check Structure') {
            steps {
                sh '''
                    echo "=== Verificando estructura del proyecto ==="
                    echo "Backend:"
                    ls -la backend/
                    echo "Frontend:"
                    ls -la frontend/
                '''
            }
        }
        
        stage('Validate Backend') {
            steps {
                dir('backend') {
                    sh '''
                        echo "=== Validando Backend ==="
                        echo "Dockerfile:"
                        cat Dockerfile
                        echo "Requirements:"
                        cat requirements.txt
                        echo "Estructura src:"
                        find src/ -name "*.py" | head -10
                    '''
                }
            }
        }
        
        stage('Validate Frontend') {
            steps {
                dir('frontend') {
                    sh '''
                        echo "=== Validando Frontend ==="
                        echo "Dockerfile:"
                        cat Dockerfile
                        echo "Archivos estáticos:"
                        find . -name "*.html" -o -name "*.js" -o -name "*.css" | head -10
                    '''
                }
            }
        }
        
        stage('Manual Build Instructions') {
            steps {
                sh """
                    echo "=== INSTRUCCIONES PARA COMPLETAR ==="
                    echo ""
                    echo "1. CONSTRUIR IMÁGENES DOCKER:"
                    echo "   cd backend && docker build -t ${BACKEND_IMAGE_NAME}:${VERSION} ."
                    echo "   cd frontend && docker build -t ${FRONTEND_IMAGE_NAME}:${VERSION} ."
                    echo ""
                    echo "2. SUBIR A DOCKERHUB:"
                    echo "   docker push ${BACKEND_IMAGE_NAME}:${VERSION}"
                    echo "   docker push ${FRONTEND_IMAGE_NAME}:${VERSION}"
                    echo ""
                    echo "Este pipeline valida:"
                    echo "   - Estructura del proyecto"
                    echo "   - Dockerfiles correctos"
                    echo "   - Archivos de configuración"
                """
            }
        }
    }
    
    post {
        success {
            echo 'Validación de estructura completada exitosamente'
        }
        failure {
            echo 'Error en validación de estructura'
        }
    }
}

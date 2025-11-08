pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        BACKEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-backend'
        FRONTEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-frontend'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        // Stage 1: Build Backend
        stage('Build Backend') {
            steps {
                echo 'Construyendo Backend...'
                dir('backend') {
                    // Validación rápida de imports
                    sh '''
                        echo "=== Validación Backend ==="
                        cd src
                        python -c "
                        import sys
                        sys.path.append('.')
                        try:
                            from ReverseMortgage import MonthlyPayment
                            from model.User import User  
                            from controller.Controlador_usuarios import ClientController
                            print('Todos los imports funcionan')
                        except Exception as e:
                            print('Error en imports:', e)
                            sys.exit(1)
                        "
                    '''
                    // Build directo de Docker
                    sh "docker build -t ${BACKEND_IMAGE_NAME}:${VERSION} ."
                }
            }
        }
        
        // Stage 2: Build Frontend
        stage('Build Frontend') {
            steps {
                echo 'Construyendo Frontend...'
                dir('frontend') {
                    // Validación mínima
                    sh '''
                        echo "=== Validación Frontend ==="
                        if [ -f "index.html" ]; then
                            echo "index.html encontrado"
                        else
                            echo "ERROR: index.html no existe"
                            exit 1
                        fi
                    '''
                    sh "docker build -t ${FRONTEND_IMAGE_NAME}:${VERSION} ."
                }
            }
        }
        
        // Stage 3: Push a DockerHub
        stage('Push to DockerHub') {
            steps {
                echo 'Subiendo a DockerHub...'
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        sh "docker push ${BACKEND_IMAGE_NAME}:${VERSION}"
                        sh "docker push ${FRONTEND_IMAGE_NAME}:${VERSION}"
                    }
                }
            }
        }
        
        // Stage 4: Verificación final
        stage('Verify') {
            steps {
                echo 'Verificación final...'
                sh """
                    echo "¡PIPELINE COMPLETADO!"
                    echo ""
                    echo "IMÁGENES CREADAS:"
                    echo "Backend:  ${BACKEND_IMAGE_NAME}:${VERSION}"
                    echo "Frontend: ${FRONTEND_IMAGE_NAME}:${VERSION}"
                    echo ""
                    echo "PARA PROBAR:"
                    echo "Backend:  docker run -p 5000:5000 ${BACKEND_IMAGE_NAME}:${VERSION}"
                    echo "Frontend: docker run -p 80:80 ${FRONTEND_IMAGE_NAME}:${VERSION}"
                """
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finalizado.'
            sh 'docker system prune -f || true'
        }
        success {
            echo '¡Éxito! Imágenes en DockerHub'
        }
        failure {
            echo 'Pipeline falló'
        }
    }
}

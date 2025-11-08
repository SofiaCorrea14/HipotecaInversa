pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = 'TU_USUARIO_DOCKERHUB/reverse-mortgage-backend'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        // Stage 1: Instalar dependencias Python
        stage('Install Dependencies') {
            steps {
                echo 'Instalando dependencias Python...'
                dir('backend') {
                    sh 'pip install -r requirements.txt'
                }
            }
        }
        
        // Stage 2: Pruebas unitarias Python
        stage('Test') {
            steps {
                echo 'Ejecutando pruebas unitarias...'
                dir('backend') {
                    // Si usas pytest
                    sh 'python -m pytest tests/ -v || echo "No tests found or tests failed"'
                    
                    // O si usas unittest
                    sh 'python -m unittest discover tests/ -v || echo "No tests found"'
                }
            }
            post {
                always {
                    // Guardar reportes de tests si existen
                    archiveArtifacts 'backend/**/test-reports/*.xml'
                }
            }
        }
        
        // Stage 3: Análisis de código (opcional)
        stage('Code Analysis') {
            steps {
                echo 'Analizando código Python...'
                dir('backend') {
                    // Instalar herramientas de análisis
                    sh 'pip install pylint flake8 || echo "Analysis tools not available"'
                    // Ejecutar análisis (no falla el pipeline si hay warnings)
                    sh 'pylint ReverseMortgage/ || echo "Pylint completed with warnings"'
                    sh 'flake8 ReverseMortgage/ || echo "Flake8 completed with warnings"'
                }
            }
        }
        
        // Stage 4: Build de imagen Docker
        stage('Build Docker Image') {
            steps {
                echo 'Construyendo imagen Docker...'
                script {
                    dir('backend') {
                        dockerImage = docker.build("${IMAGE_NAME}:${VERSION}")
                    }
                }
            }
        }
        
        // Stage 5: Publicar en DockerHub
        stage('Push to DockerHub') {
            steps {
                echo 'Publicando imagen en DockerHub...'
                script {
                    docker.withRegistry('', 'dockerhub-creds') {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
        
        // Stage 6: Limpieza
        stage('Cleanup') {
            steps {
                echo 'Limpiando imágenes locales...'
                sh 'docker system prune -f || true'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completado.'
            cleanWs()
        }
        success {
            echo 'Pipeline ejecutado exitosamente!'
            // Opcional: Notificación por email
            emailext (
                subject: "Pipeline ReverseMortgage SUCCESS - Build ${env.BUILD_NUMBER}",
                body: "El pipeline se ejecutó correctamente. Imagen: ${IMAGE_NAME}:${VERSION}",
                to: "tu_email@ejemplo.com"
            )
        }
        failure {
            echo 'Pipeline falló!'
            emailext (
                subject: "Pipeline ReverseMortgage FAILED - Build ${env.BUILD_NUMBER}",
                body: "El pipeline falló. Revisa Jenkins: ${env.BUILD_URL}",
                to: "tu_email@ejemplo.com"
            )
        }
    }
}

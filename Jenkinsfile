pipeline {
    agent any
    
    environment {
        BACKEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-backend'
        FRONTEND_IMAGE_NAME = 'sofiac14/reverse-mortgage-frontend'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        // Stage 1: Build 
        stage('Build Only') {
            steps {
                echo 'CONSTRUYENDO IMÁGENES...'
                
                // Backend
                dir('backend') {
                    sh '''
                        echo "=== Validando Backend ==="
                        cd src
                        python -c "
                        import sys
                        sys.path.append('.')
                        try:
                            from ReverseMortgage import MonthlyPayment
                            from model.User import User  
                            from controller.Controlador_usuarios import ClientController
                            print('Backend - Imports OK')
                        except Exception as e:
                            print('Error backend:', e)
                            sys.exit(1)
                        "
                    '''
                    sh "docker build -t ${BACKEND_IMAGE_NAME}:${VERSION} ."
                }
                
                // Frontend
                dir('frontend') {
                    sh '''
                        echo "=== Validando Frontend ==="
                        if [ -f "index.html" ]; then
                            echo "Frontend - index.html OK"
                        else
                            echo "ERROR: index.html no existe"
                            exit 1
                        fi
                    '''
                    sh "docker build -t ${FRONTEND_IMAGE_NAME}:${VERSION} ."
                }
                
                echo "¡BUILD COMPLETADO!"
                echo "Backend: ${BACKEND_IMAGE_NAME}:${VERSION}"
                echo "Frontend: ${FRONTEND_IMAGE_NAME}:${VERSION}"
            }
        }
        
        
        stage('Instructions') {
            steps {
                echo 'INSTRUCCIONES PUSH MANUAL:'
                sh """
                    echo "Para subir a DockerHub manualmente:"
                    echo "1. docker login -u sofiac14"
                    echo "2. docker push ${BACKEND_IMAGE_NAME}:${VERSION}"
                    echo "3. docker push ${FRONTEND_IMAGE_NAME}:${VERSION}"
                    echo ""
                    echo "Esto demuestra que el pipeline FUNCIONA"
                    echo "El push automático requiere configurar permisos en Jenkins"
                """
            }
        }
    }
    
    post {
        success {
            echo '¡Pipeline ejecutado exitosamente!'
            
        }
    }
}

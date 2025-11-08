pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = 'sofiac14/hipotecainversa'
        VERSION = "${env.BUILD_ID}"
    }
    
    stages {
        // Stage 1: Compilación
        stage('Compile') {
            steps {
                echo 'Compilando el proyecto Hipoteca Inversa...'
                sh 'mvn clean compile'
            }
        }
        
        // Stage 2: Pruebas unitarias
        stage('Test') {
            steps {
                echo 'Ejecutando pruebas unitarias...'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        // Stage 3: Empaquetado
        stage('Package') {
            steps {
                echo 'Empaquetando aplicación...'
                sh 'mvn package -DskipTests'
            }
        }
        
        // Stage 4: Build de imagen Docker
        stage('Build Docker Image') {
            steps {
                echo 'Construyendo imagen Docker...'
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${VERSION}")
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
    }
    
    post {
        always {
            echo 'Pipeline completado.'
        }
        success {
            echo 'Pipeline ejecutado exitosamente!'
        }
        failure {
            echo 'Pipeline falló!'
        }
    }
}

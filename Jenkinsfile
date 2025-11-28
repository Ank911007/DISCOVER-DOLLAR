pipeline {
    agent any
    
    environment {
        DOCKERHUB_USER = "ank906"
        FRONTEND_IMAGE = "ank906/frontend-app"
        BACKEND_IMAGE = "ank906/backend-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Git Cheeckout') {
            steps {
                git branch: 'main', url: 'https://github.com/Ank911007/DISCOVER-DOLLAR.git'
            }
        }
        
         stage('Build Docker Images') {
            steps {
                script {
                    sh "docker build -t ${FRONTEND_IMAGE}:${TAG} ./frontend"
                    sh "docker build -t ${BACKEND_IMAGE}:${TAG} ./backend"
                }
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                }
            }
        }
        
         stage('Push Images to Docker Hub') {
            steps {
                sh "docker push ${FRONTEND_IMAGE}:${TAG}"
                sh "docker push ${BACKEND_IMAGE}:${TAG}"
            }
        }
        
        stage('Update Docker Compose With Latest Tag') {
    steps {
        script {
            sh """
            cd ${WORKSPACE}

            # Replace tags with latest BUILD_NUMBER
            sed -i "s|ank906/frontend-app:.*|ank906/frontend-app:${TAG}|g" docker-compose.yaml
            sed -i "s|ank906/backend-app:.*|ank906/backend-app:${TAG}|g" docker-compose.yaml
                    """
        }
    }
}
        
        stage('Deploy Using Docker Compose') {
            steps {
                script {
                    sh """
                    cd ${WORKSPACE}
                    docker-compose pull
                    docker-compose down
                    docker-compose up -d
                    """
                }
            }
        }
    }
}

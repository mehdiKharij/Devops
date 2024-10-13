pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'd3f85899-0f75-4f08-a9d4-a9b574cb5d55' // Remplacez par l'ID de vos credentials Docker dans Jenkins
    }

    stages {
        stage('Build Frontend') {
            steps {
                script {
                    // Construire l'image frontend
                    echo 'Building Frontend...'
                    bat 'docker build -t awwin/app-devops:frontend -f angular-17-client/Dockerfile .'
                }
            }
        }
        stage('Build Backend') {
            steps {
                script {
                    // Construire l'image backend
                    echo 'Building Backend...'
                    bat 'docker build -t awwin/app-devops:1.0 -f spring-boot-server/Dockerfile .'
                }
            }
        }
        stage('Push Frontend to Docker Hub') {
            steps {
                script {
                    // Pousser l'image frontend vers Docker Hub
                    echo 'Pushing Frontend to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        bat "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                        bat 'docker push awwin/app-devops:frontend'
                    }
                }
            }
        }
        stage('Push Backend to Docker Hub') {
            steps {
                script {
                    // Pousser l'image backend vers Docker Hub
                    echo 'Pushing Backend to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        bat "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                        bat 'docker push awwin/app-devops:1.0'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
                // Ajoutez ici les commandes nécessaires pour déployer vos conteneurs
                // par exemple, utiliser docker run ou docker-compose
            }
        }
    }

    post {
        success {
            echo 'Build and Push Successful!'
        }
        failure {
            echo 'Build or Push Failed!'
        }
    }
}

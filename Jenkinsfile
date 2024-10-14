pipeline {
    agent any  
    stages {
        stage('Checkout Code') {
            steps {
                // Vérifier le code source à partir du dépôt
                git branch: 'main', url: 'https://github.com/mehdiKharij/Devops.git'
            }
        }

        stage('Build Frontend') {
            steps {
                dir('angular-17-client') {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }
        
        stage('Build Backend') {
            steps {
                dir('spring-boot-server') {
                     bat '"C:\\Program Files\\apache-maven-3.9.9\\bin\\mvn" clean install'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Construire les images Docker pour le frontend et le backend
                    echo 'Building Docker images...'
                     bat 'docker build -t myapp/frontend:latest -f angular-17-client/Dockerfile .'
                    bat 'docker build -t myapp/backend:latest --build-arg JAR_FILE=spring-boot-server/target/spring-boot-jpa-h2-0.0.1-SNAPSHOT.jar -f spring-boot-server/Dockerfile .'
                }
            }
        }

        stage('Run Docker Containers') {
            steps {
                script {
                    // Arrêter et supprimer les conteneurs s'ils existent déjà
                    bat 'docker rm -f backend || exit 0'

                    // Démarrer les nouveaux conteneurs
                    echo 'Running Docker containers...'
                    bat 'docker run -d --name frontend -p 80:80 myapp/frontend:latest'
                    bat 'docker run -d --name backend -p 81:8081 myapp/backend:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

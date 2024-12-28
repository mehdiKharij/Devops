pipeline {
    agent any  

    environment {
        // Définissez les variables pour SSH et EC2
        EC2_USER = 'ec2-user'  // Utilisez votre utilisateur EC2 (par exemple 'ubuntu' ou 'ec2-user')
        EC2_HOST = '13.60.58.155'  // Remplacez par l'adresse IP publique de votre instance EC2
        SSH_KEY_PATH = 'C:/users/user/my-ec2-key.pem'  // Remplacez par le chemin de votre clé privée
        DEPLOY_DIR = '/home/ec2-user/app'  // Répertoire sur EC2 pour stocker les fichiers Docker
    }

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
                    bat 'docker rm -f frontend || exit 0'
                    bat 'docker rm -f backend || exit 0'

                    // Démarrer les nouveaux conteneurs
                    echo 'Running Docker containers locally...'
                    bat 'docker run -d --name frontend -p 80:80 myapp/frontend:latest'
                    bat 'docker run -d --name backend -p 81:8081 myapp/backend:latest'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    echo 'Deploying Docker containers to EC2...'

                    // Copier le docker-compose.yml et les autres fichiers nécessaires vers EC2
                    sh """
                        scp -i ${SSH_KEY_PATH} -o StrictHostKeyChecking=no docker-compose.yml ${EC2_USER}@${EC2_HOST}:${DEPLOY_DIR}
                        scp -i ${SSH_KEY_PATH} -o StrictHostKeyChecking=no angular-17-client/Dockerfile ${EC2_USER}@${EC2_HOST}:${DEPLOY_DIR}
                        scp -i ${SSH_KEY_PATH} -o StrictHostKeyChecking=no spring-boot-server/Dockerfile ${EC2_USER}@${EC2_HOST}:${DEPLOY_DIR}
                    """

                    // Exécuter docker-compose sur l'instance EC2 pour démarrer les containers
                    sh """
                        ssh -i ${SSH_KEY_PATH} -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << 'EOF'
                            cd ${DEPLOY_DIR}
                            docker-compose down  # Arrêter et supprimer les containers existants
                            docker-compose pull  # Mettre à jour les images Docker
                            docker-compose up -d  # Démarrer les containers en mode détaché
                        EOF
                    """
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

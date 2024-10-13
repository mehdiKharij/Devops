pipeline {
    agent any  
    stages {
        stage('Checkout Code') {
            steps {
                // Vérifier le code source à partir du dépôt
                git branch: 'main', url: 'https://github.com/Fzsalhi2002/Devops.git'
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
                    bat 'mvn clean install'
                }
            }
        }

        }
       
}

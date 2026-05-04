pipeline {
    agent any
    tools {
        maven 'maven-3.9.6'
        dockerTool 'docker'
    }
    stages {
        stage('Packaging') {
            steps {
                echo 'Packaging..'
                sh 'mvn clean package'
            }
        }
        stage('Copying war file') {
            steps {
                echo 'Copying war file..'
                // Saca el archivo .war de target para que el Dockerfile lo vea
                sh 'mv target/*.war .'
            }
        }
        stage('cleanup') {
            steps {
                echo 'Cleaning up old containers...'
                // Detiene y borra el contenedor anterior si existe para evitar conflictos
                sh 'docker stop devops-web-project-server || true'
                sh 'docker rm devops-web-project-server || true'
                sh 'docker system prune -f --filter "label=devops-web-projectserver"'
            }
        }
        stage('build image') {
            steps {
                echo 'Building Docker image...'
                // Crea la imagen usando tu usuario de Docker Hub
                sh 'docker build -t milacasas/devops-web-project:v1 --label devops-web-projectserver .'
            }
        }
        stage('run container') {
            steps {
                echo 'Starting container on port 8081...'
                // Lanza el contenedor mapeando el puerto 8081 al 8080 interno
                sh 'docker run -d --name devops-web-project-server --label devops-web-projectserver -p 8081:8080 milacasas/devops-web-project:v1'
            }
        }
    }
}

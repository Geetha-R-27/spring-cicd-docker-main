pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "geethar27/springapp:latest"
        APP_PORT = "80"
        COMPOSE_FILE = "docker-compose.yml"
    }
    
    tools {
        maven 'maven'
    }

    stages {

        stage('Build & Package') {
            steps {
                echo "Building the Spring Boot application"
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image (Manual)') {
            steps {
                echo "Building Docker image manually"
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Stop Existing Container (Manual)') {
            steps {
                echo "Stopping existing container if running"
                sh """
                if [ \$(docker ps -q -f name=springapp) ]; then
                    docker stop springapp
                    docker rm springapp
                fi
                """
            }
        }

        stage('Run Docker Container (Manual)') {
            steps {
                echo "Starting Spring Boot container manually"
                sh "docker run -d --name springapp -p ${APP_PORT}:80 ${DOCKER_IMAGE}"
            }
        }

        stage('Stop Existing Containers (Compose)') {
            steps {
                echo "Stopping existing containers via Docker Compose"
                sh """
                if [ -f ${COMPOSE_FILE} ]; then
                    docker-compose -f ${COMPOSE_FILE} down
                fi
                """
            }
        }

        stage('Build & Run Docker Compose') {
            steps {
                echo "Building and starting containers using Docker Compose"
                sh "docker-compose -f ${COMPOSE_FILE} up --build -d"
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed. Check logs for errors."
        }
    }
}

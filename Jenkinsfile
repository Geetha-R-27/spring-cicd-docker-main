pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "geethar27/springapp:latest"
        APP_PORT = "9091"                   // new port for this project
        CONTAINER_NAME = "springapp-new"    // new container name
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

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image"
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }

        stage('Stop Existing Container') {
            steps {
                echo "Stopping existing container if running"
                sh """
                if [ \$(docker ps -q -f name=${CONTAINER_NAME}) ]; then
                    docker stop ${CONTAINER_NAME}
                    docker rm ${CONTAINER_NAME}
                fi
                """
            }
        }

        stage('Stop Existing Containers (Compose)') {
            steps {
                echo "Stopping existing containers via Docker Compose"
                sh """
                if [ -f ${COMPOSE_FILE} ]; then
                    docker-compose -f ${COMPOSE_FILE} down --remove-orphans
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

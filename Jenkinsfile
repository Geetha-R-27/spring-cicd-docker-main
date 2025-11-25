pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "geethar27/springapp:latest"
        APP_PORT = "80"
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
                if [ \$(docker ps -q -f name=springapp) ]; then
                    docker stop springapp
                    docker rm springapp
                fi
                """
            }
        }

        stage('Run Docker Container') {
            steps {
                echo "Starting Spring Boot container"
                sh "docker run -d --name springapp -p ${APP_PORT}:80 ${DOCKER_IMAGE}"
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed."
        }
    }
}

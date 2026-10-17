pipeline {

    agent any

    environment {
        DOCKER_USER = "believer14"

        BACKEND_IMAGE = "believer14/student-task-backend"
        FRONTEND_IMAGE = "believer14/student-task-frontend"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Docker Login") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "dockerhubcred",
                        usernameVariable: "DOCKER_USERNAME",
                        passwordVariable: "DOCKER_PASSWORD"
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        stage("Build Backend") {
            steps {
                sh '''
                    docker build \
                    -t ${BACKEND_IMAGE}:latest \
                    ./backend
                '''
            }
        }

        stage("Build Frontend") {
            steps {
                sh '''
                    docker build \
                    -t ${FRONTEND_IMAGE}:latest \
                    ./frontend
                '''
            }
        }

        stage("Push Images") {
            steps {
                sh '''
                    docker push ${BACKEND_IMAGE}:latest
                    docker push ${FRONTEND_IMAGE}:latest
                '''
            }
        }
    }

    post {

        success {
            echo "Docker images built and pushed successfully!"
        }

        failure {
            echo "Pipeline failed!"
        }

        always {
            sh '''
                docker logout || true
            '''
        }
    }
}
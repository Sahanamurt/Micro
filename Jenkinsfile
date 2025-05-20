pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        DOCKERHUB_USERNAME = "sahana042"
    }

    stages {
        stage("Clean") {
            steps {
                sh 'mvn clean'
            }
        }

        stage("Validate") {
            steps {
                sh 'mvn validate'
            }
        }

        stage("Test") {
            steps {
                sh 'mvn test'
            }
        }

        stage("Package") {
            steps {
                sh 'mvn package'
            }
            post {
                success {
                    echo "Build successful"
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                sh 'docker build -t phonepe .'
            }
            post {
                success {
                    echo "Docker image built successfully"
                }
                failure {
                    echo "Docker image build failed"
                }
            }
        }

        stage("Push to Docker Hub") {
            steps {
                sh """
                    docker tag phonepe ${DOCKERHUB_USERNAME}/phonepe
                    docker push ${DOCKERHUB_USERNAME}/phonepe
                """
            }
            post {
                success {
                    echo "Docker image pushed successfully"
                }
                failure {
                    echo "Docker image push failed"
                }
            }
        }

        stage("Remove Docker Images Locally") {
            steps {
                sh """
                    docker rmi -f ${DOCKERHUB_USERNAME}/phonepe || true
                    docker rmi -f phonepe || true
                """
            }
            post {
                success {
                    echo "Local Docker images removed successfully"
                }
                failure {
                    echo "Failed to remove local Docker images"
                }
            }
        }

        stage("Stop and Restart Container") {
            steps {
                sh """
                    docker rm -f app || true
                    docker run -it -d --name app -p 8081:8080 ${DOCKERHUB_USERNAME}/phonepe
                """
            }
            post {
                success {
                    echo "Container restarted successfully"
                }
                failure {
                    echo "Failed to restart container"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful"
        }
        failure {
            echo "Deployment failed"
        }
    }
}

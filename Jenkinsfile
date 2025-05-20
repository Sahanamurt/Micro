Jenkins file code:                                                                                                                                      pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment{
        DOCKERHUB_USERNAME = "sahana042"
    }
    stages {
        stage("clean") {
            steps {
                sh 'mvn clean'
            }
        }
        stage("validate") {
            steps {
                sh 'mvn validate'
            }
        }
        stage("test") {
            steps {
                sh 'mvn test'
            }
        }
        stage("package") {
            steps {
                sh 'mvn package'
            }
            post {
                success {
                    echo "build successfull"
                }
            }
        }
        stage("build docker images") {
            steps {
                sh 'docker build -t phonepe .'
            }
            post {
                success{
                    echo "image build successfully"
                }
                failure{
                    echo "image not built"
                }
            }
        }
        stage("push to docker hub"){
            steps{
                script {
                    sh"""
                    docker tag phonepe ${DOCKERHUB_USERNAME}/phonepe
                    docker push ${DOCKERHUB_USERNAME}/phonepe
                    """
                }
                post {
                success{
                    echo "image pushed successfully"
                }
                failure{
                    echo "image not pushed"
                }

            }
                
        }
        stage("remove docker image locally"){
            steps{
                sh"""
                docker rmi -f ${DOCKERHUB_USERNAME}/phonepe
                docker rmi -f phonepe
                """
            }
            post {
                success{
                    echo "image removed successfully"
                }
                failure{
                    echo "image not removed"
                }
        }
        stage("stop and restart"){
            steps {
                sh"""
                docker rm -f app
                docker run -it -d --name app -p 8081:8080 ${DOCKERHUB_USERNAME}/phonepe
                """
            }
            post {
                success{
                    echo "image stoped successfully"
                }
                failure{
                    echo "image not stoped"
                }
        }
    }
    post {
        success {
            echo "deployemnt successfull"
        }
        failure {
            echo "deployment is failure"
        }
    }
}
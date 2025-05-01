pipeline {
    agent any

    environment {
        IMAGE_NAME = 'jaikp/java-rest-api'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Jaikp/Scenario2.git'
            }
        }

        stage('Build & Test') {
            agent {
                docker {
                    image 'maven:3.8.7-eclipse-temurin-17'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh 'docker push $IMAGE_NAME'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker ps -q --filter "ancestor=$IMAGE_NAME" | xargs -r docker stop || true'
                sh 'docker run -d -p 8080:8080 $IMAGE_NAME'
            }
        }
    }
}

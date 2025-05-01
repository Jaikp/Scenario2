pipeline {
    agent any  // use default Jenkins node (ensure it has Docker installed)
    
    environment {
        IMAGE_NAME = 'jaikp0/java-rest-api'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Jaikp/Scenario2.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
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

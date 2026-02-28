pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "iammanali/flask-shopping-app"
        VERSION = "${BUILD_NUMBER}"
        APP_SERVER = "ubuntu@16.176.6.206"
    }

    stages {

        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$VERSION ./shopping-application'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                 usernameVariable: 'USERNAME',
                                 passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }

        stage('Push Versioned Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE:$VERSION'
            }
        }

        stage('Update Latest Tag') {
            steps {
                sh '''
                docker tag $DOCKER_IMAGE:$VERSION $DOCKER_IMAGE:latest
                docker push $DOCKER_IMAGE:latest
                '''
            }
        }

        stage('Deploy to App Server') {
            steps {
                sh """
                ssh -o StrictHostKeyChecking=no $APP_SERVER '
                cd aws-jenkins-docker-3tier-project &&
                docker compose pull &&
                docker compose up -d
                '
                """
            }
        }
    }
}
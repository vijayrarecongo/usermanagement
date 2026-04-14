pipeline {
    agent any

    environment {
        IMAGE_NAME = "vijayrarecongo/usermanagement:latest"
    }

    stages {

        stage('Clean Workspace') {
            steps {
                sh 'rm -rf * .* || true'
            }
        }

        stage('Clone') {
            steps {
                git 'https://github.com/vijayrarecongo/usermanagement.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t usermanagement-application-image:latest .'
            }
        }

        stage('Docker Tag') {
            steps {
                sh 'docker tag usermanagement-application-image:latest $IMAGE_NAME'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker compose down || true
                    docker compose up -d
                '''
            }
        }

        stage('Cleanup') {
            steps {
                sh 'rm -rf * .* || true'
            }
        }
    }
}
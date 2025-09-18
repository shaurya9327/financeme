pipeline {
    agent { label 'worker' }

    environment {
        DOCKER_IMAGE = "shaurya93/financeme:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/shaurya9327/financeme.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-token', variable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u shaurya93 --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker stop financeme || true'
                sh 'docker rm financeme || true'
                sh 'docker run -d -p 8081:8081 --name financeme $DOCKER_IMAGE'
            }
        }
    }
}

pipeline {
    agent { label 'worker-node' } // Must match your worker node label

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // ID of your DockerHub credentials in Jenkins
        IMAGE_NAME = "shaurya93/financeme"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/shaurya9327/financeme.git', branch: 'master'
            }
        }

        stage('Build with Maven') {
            steps {
                withMaven(maven: 'Maven-3.9.0') { // Change to your Maven tool name in Jenkins
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sh "docker stop financeme || true"
                sh "docker rm financeme || true"
                sh "docker run -d -p 8081:8081 --name financeme ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

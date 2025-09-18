pipeline {
    agent { label 'worker-node' }  // Run on your worker node

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds') // Jenkins credential ID for DockerHub
        IMAGE_NAME = 'shaurya93/financeme:latest'
    }

    tools {
        maven 'Maven-3.9.111' // Must match the Maven installation name in Jenkins Global Tool Configuration
        jdk 'JDK-11'           // Assuming JDK-11 is installed in Jenkins
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/shaurya9327/financeme.git', branch: 'master', credentialsId: 'c612d624-6d01-4fb8-ad68-a6ab575d07aa'
            }
        }

        stage('Build with Maven') {
            steps {
                withMaven(maven: 'Maven-3.9.111') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "sudo docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS --password-stdin"
                    sh "sudo docker push ${IMAGE_NAME}"
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    sh "sudo docker stop financeme || true"
                    sh "sudo docker rm financeme || true"
                    sh "sudo docker run -d -p 8081:8081 --name financeme ${IMAGE_NAME}"
                }
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}

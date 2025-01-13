pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code...'
                checkout scm
            }
        }

        stage('Build with Maven Tools') {
            steps {
                echo 'Building the application using Maven...'
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building the Docker image...'
                sh 'docker build -t vinayak192/tomcat:v4 .'
            }
        }

        stage('Push Docker Image') {
            environment {
                DOCKER_CREDENTIALS_ID = 'your-docker-credentials-id' // Replace with Jenkins credentials ID
            }
            steps {
                echo 'Pushing the Docker image to Docker Hub...'
                withDockerRegistry(credentialsId: "${DOCKER_CREDENTIALS_ID}", url: '') {
                    sh 'docker push vinayak192/tomcat:v4'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application using Docker...'
                sh '''
                docker pull vinayak192/tomcat:v4
                docker stop my-tomcat || true
                docker rm my-tomcat || true
                docker run -d --name my-tomcat -p 80:8080 vinayak192/tomcat:v4
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
            // Trigger another Jenkins job if needed
            script {
                try {
                    build job: 'deployment', propagate: true // Replace 'deployment' with the actual job name
                } catch (Exception e) {
                    echo "Error triggering deployment job: ${e.message}"
                }
            }
        }
        failure {
            echo 'Pipeline failed. Please check the logs for more details.'
        }
    }
}

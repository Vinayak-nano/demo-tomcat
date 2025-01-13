pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-hub' // Replace with your Docker Hub credentials ID
        DOCKER_IMAGE = 'vinayak192/tomcat'
        IMAGE_TAG = 'v4' // or 'v1' for versioning
    }

    stages {
        stage('Build with Maven tools') {
            steps {
                script {
                    // Ensure Maven is installed on your Jenkins agent
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_HUB_CREDENTIALS}") {
                        sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Example: Deploying using Docker run
                    sh """
                    docker pull ${DOCKER_IMAGE}:${IMAGE_TAG}
                    docker stop my-tomcat || true
                    docker rm my-tomcat || true
                    docker run -d --name my-tomcat -p 80:8080 ${DOCKER_IMAGE}:${IMAGE_TAG}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully! Triggering the deployment job...'
            // Trigger the 'deployment' job after success
            build job: 'deployment', wait: false
        }
        failure {
            echo 'Pipeline failed. Please check the logs for errors.'
        }
    }
}

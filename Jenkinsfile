pipeline {
    agent any

    environment {
        IMAGE_NAME = "manojkrishnappa/checkoutservice:${GIT_COMMIT}"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/ITkannadigaru/checkoutservice.git', branch: 'main'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'go test ./...'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    printenv
                    docker build -t ${IMAGE_NAME} .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh "docker push ${IMAGE_NAME}"
            }
        }

    }

    post {
        always {
            sh "docker rmi ${IMAGE_NAME} || true"
            sh "docker logout || true"
        }
        success {
            echo "Build and push successful: ${IMAGE_NAME}"
        }
        failure {
            echo "Pipeline failed. Check the logs above."
        }
    }
}
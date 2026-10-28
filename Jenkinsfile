pipeline {
    agent any

    environment {
        DOCKER_HOST = 'tcp://docker:2375'
        IMAGE_NAME = 'mlops-python-demo'
        CONTAINER_NAME = 'mlops-python-app'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} python -m unittest -v'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true
                    docker run -d --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed: build, tests, and deployment succeeded.'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}

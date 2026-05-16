#Jenkinsfileforshow

pipeline {
    agent any

    environment {
        IMAGE_NAME = "deepdevops11/jenkins-demo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Build') {
            steps {

                echo "Creating virtual environment..."

                sh '''
                python3 -m venv venv

                . venv/bin/activate

                pip install --upgrade pip

                pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {

                echo "Running test cases..."

                sh '''
                . venv/bin/activate
                pytest
                '''
            }
        }

        stage('Docker Build') {
            steps {

                echo "Building Docker Image..."

                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login & Push') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'

                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }

        stage('Run Container') {
            steps {

                sh '''
                docker stop demo-container || true

                docker rm demo-container || true

                docker run -d \
                  --name demo-container \
                  -p 5000:5000 \
                  $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    post {

        success {
            echo 'Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}

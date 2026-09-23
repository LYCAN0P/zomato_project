pipeline {

    agent any

    environment {
        DOCKER_IMAGE = 'lycan1/zomato'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing npm dependencies...'
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'CI=true npm test -- --watchAll=false --passWithNoTests'
            }
        }

        stage('Build React App') {
            steps {
                echo 'Building React application...'
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
                sh 'docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest'
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-lycan1',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin

                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Zomato CI/CD pipeline completed successfully!'
        }

        failure {
            echo 'Zomato CI/CD pipeline failed. Check the console output.'
        }
    }
}

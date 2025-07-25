pipeline {
    agent { label 'slave' }

    environment {
        IMAGE_NAME = "legpro/spring"
        IMAGE_TAG = "1.0"
        DEPLOY_DIR = "deploy"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/LegPro/SprintBootService-1.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh '''
                mvn clean package
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                cp target/SpringBootExecutableJarFileDemo-0.0.1-SNAPSHOT.jar .
                ls -l  # Debug: verify jar and Dockerfile presence
                cat Dockerfile  # Debug: make sure it's here
                docker build -t $IMAGE_NAME:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deploy.yaml'
            }
        }
    }
}

pipeline {
    agent any

    environment {
        IMAGE = "bunny324/practice"
        TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install & Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE}:${TAG} ."
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push ${IMAGE}:${TAG}"
                }
            }
        }

        stage('Deploy to k3s') {
            steps {
                sh "kubectl set image deployment/app-deployment app=${IMAGE}:${TAG}"
                sh "kubectl rollout status deployment/app-deployment"
            }
        }
    }
}

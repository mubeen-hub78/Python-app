pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub')
        IMAGE_NAME = "mubeendochub/my_images"
        SLACK_WEBHOOK = credentials('Slack')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/mubeen-hub78/Python-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Docker Hub Login') {
            steps {
                sh """
                    echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                """
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }

        success {
            sh """
                curl -X POST -H 'Content-type: application/json' \
                --data '{\"text\":\"✅ Build Success - ${JOB_NAME} ${BUILD_NUMBER} (<${BUILD_URL}|Open>)\"}' \
                ${SLACK_WEBHOOK}
            """
        }

        failure {
            sh """
                curl -X POST -H 'Content-type: application/json' \
                --data '{\"text\":\"❌ Build Failed - ${JOB_NAME} ${BUILD_NUMBER} (<${BUILD_URL}|Open>)\"}' \
                ${SLACK_WEBHOOK}
            """
        }
    }
}

pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        DOCKER_CREDENTIALS = credentials('dockerhub-creds')  // your Docker Hub credentials ID
        IMAGE_NAME = "anassouissi/student-management"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('GIT Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/souissianas/anas_souissi_4SAE11.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh """
                   sudo docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                """
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                    echo "${DOCKER_CREDENTIALS_PSW}" | sudo docker login -u "${DOCKER_CREDENTIALS_USR}" --password-stdin
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    sudo docker push ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline successful — Image pushed to Docker Hub!"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}

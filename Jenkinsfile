pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        IMAGE_NAME = "anassouissi/student-management"
        VERSION = "${env.BUILD_ID}"
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
                    docker build -t ${IMAGE_NAME}:${VERSION} .
                    docker tag ${IMAGE_NAME}:${VERSION} ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Docker Login') {
            steps {
                sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                """
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    docker push ${IMAGE_NAME}:${VERSION}
                    docker push ${IMAGE_NAME}:latest
                """
            }
        }
    }

    post {
        success {
            echo "🎉 Pipeline successful — Image pushed to Docker Hub!"
            echo "📦 Image: ${IMAGE_NAME}:${VERSION}"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}

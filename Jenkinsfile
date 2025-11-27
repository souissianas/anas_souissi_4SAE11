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

        stage('Fix Dockerfile') {
            steps {
                sh '''
                    # Corriger le Dockerfile
                    cat > Dockerfile << 'EOF'
FROM openjdk:17.0.1-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8089
ENTRYPOINT ["java", "-jar", "app.jar"]
EOF
                    # Vérifier le contenu
                    echo "=== Dockerfile corrigé ==="
                    cat Dockerfile
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                retry(2) {
                    timeout(time: 15, unit: 'MINUTES') {
                        sh """
                            docker build -t ${IMAGE_NAME}:${VERSION} .
                            docker tag ${IMAGE_NAME}:${VERSION} ${IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh """
                    echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                    docker push ${IMAGE_NAME}:${VERSION}
                    docker push ${IMAGE_NAME}:latest
                    docker logout
                """
            }
        }
    }

    post {
        success {
            echo "🎉 SUCCÈS TOTAL !"
            echo "✅ Image Docker: ${IMAGE_NAME}:${VERSION}"
            echo "✅ Disponible sur Docker Hub !"
        }
        failure {
            echo "❌ Échec du pipeline."
        }
    }
}

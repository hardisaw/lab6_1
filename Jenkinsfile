pipeline {
    agent any

    tools {
        jdk: 'jdk-17'
        maven: 'maven-3.9'
    }

    environment {
        DOCKERHUB_USER = 'gundamzero'
        IMAGE_NAME     = 'my-java-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Mengambil source code dari repository...'
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                echo 'Menjalankan Kompilasi dan pengujian dengan Maven...]'
                sh 'mvn -B clean package'
            }
        }
        stage('Docker Build') {
            steps {
                echo "Membangun Docker Image: ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}..."
                sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
            }
        }
        
        stage('Docker PUsh') {
            steps {
                script {
                    echo "Mengirim Image ke Docker Registry"

                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-cred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariaable: 'DOCKER_PASS'
                    )]) {
                        sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                        sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}"
                        sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest"
                    }
                }
            }
        }

    }
    post {
        always {
            sh 'docker logout || true'
        }
        success {
            echo "Pipeline Lab 6.1 Berhasil! Artefak JAR telah dibungkus ke Docker Image"
        }
        failure {
            echo "Pipeline Lab 6.1 Gagal! Periksa log kesalahan di atas ."
        }
    }

}
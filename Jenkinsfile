pipeline {
    agent any

    environment {
        IMAGE = 'sachin0123456789/my-web-project'
    }

    stages {
        stage('Pull Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_TOKEN'
                )]) {
                    sh '''
                        echo "$DH_TOKEN" | docker login \
                        -u "$DH_USER" --password-stdin
                        docker push "$IMAGE:latest"
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy Locally') {
            steps {
                sh '''
                    docker pull "$IMAGE:latest"
                    docker rm -f sachin-web-v2 || true
                    docker run -d --name sachin-web-v2 \
                    -p 8080:80 "$IMAGE:latest"
                '''
            }
        }
    }
}

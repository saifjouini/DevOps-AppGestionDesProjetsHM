
pipeline {
    agent any

    stages {
        stage('Git clone') {
            steps {
                git branch: 'main',
                    credentialsId: 'GIT_HUB_CREDENTIALS',
                    url: 'https://github.com/saifjouini/DevOps-AppGestionDesProjetsHM.git'
            }
        }

        stage('Build backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build frontend') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run build -- --configuration production'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -f backend/Dockerfile -t saifmehdi/backend:latest .'
                sh 'docker build -f frontend/Dockerfile -t saifmehdi/frontend:latest frontend'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'DOCKERHUB_CREDENTIALS',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        set -e
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        trap 'docker logout' EXIT
                        docker push saifmehdi/backend:latest
                        docker push saifmehdi/frontend:latest
                    '''
                }
            }
        }
    }
}


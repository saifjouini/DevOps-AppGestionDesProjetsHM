
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

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool(
                        name: 'SonarScanner',
                        type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    )

                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=DevOps-AppGestionDesProjets \
                              -Dsonar.projectName=DevOps-AppGestionDesProjets \
                              -Dsonar.sources=backend/src/main/java,frontend/src \
                              -Dsonar.java.binaries=backend/target/classes \
                              -Dsonar.exclusions=**/node_modules/**,**/dist/**,**/target/**
                        """
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    docker build -f backend/Dockerfile \
                        -t saifmehdi/backend:latest .

                    docker build -f frontend/Dockerfile \
                        -t saifmehdi/frontend:latest frontend
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'DOCKERHUB_CREDENTIALS',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        set -e

                        echo "$DOCKER_PASS" | docker login \
                            -u "$DOCKER_USER" --password-stdin

                        trap 'docker logout' EXIT

                        docker push saifmehdi/backend:latest
                        docker push saifmehdi/frontend:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker compose -p devops-appgestiondesprojets pull backend frontend

                    docker compose -p devops-appgestiondesprojets up -d \
                        --force-recreate backend frontend
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline executed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed. Check the Console Output.'
        }
    }
}



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
    }
}


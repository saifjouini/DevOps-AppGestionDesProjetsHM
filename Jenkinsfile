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

        stage('Build') {
            steps {
                sh 'echo "Build du projet"'
            }
        }
    }
}

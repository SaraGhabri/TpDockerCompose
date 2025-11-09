pipeline {
    agent any

    triggers { 
        pollSCM('H/5 * * * *') 
    }

    environment {
        IMAGE_SERVER = 'sarahghabri/mern-server' // TODO: change to your Docker Hub username/repo
        IMAGE_CLIENT = 'sarahghabri/mern-client' // TODO: change to your Docker Hub username/repo
    }

    stages {

        stage('Checkout') {
            steps {
                git(
                    branch: 'main',
                    url: 'git@gitlab.com:sarah.ghabri1/mern-app.git', // TODO: change to your repo
                    credentialsId: 'gitlab_ssh' // TODO: change to your GitLab SSH credentials ID
                )
            }
        }

        stage('Build + Push SERVER') {
            when {
                changeset "server/**"
            }
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'jenkins_deploy_token', // TODO: change to your Docker Hub credentials ID
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $IMAGE_SERVER:${BUILD_NUMBER} server
                        docker push $IMAGE_SERVER:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Build + Push CLIENT') {
            when {
                changeset "client/**"
            }
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub', // TODO: change to your Docker Hub credentials ID
                        usernameVariable: 'DH_USER',
                        passwordVariable: 'DH_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                        docker build -t $IMAGE_CLIENT:${BUILD_NUMBER} client
                        docker push $IMAGE_CLIENT:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker system prune -af || true'
        }
    }
}

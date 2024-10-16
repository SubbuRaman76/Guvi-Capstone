pipeline {
    agent any
    environment {
        DEV_REPO = 'your-dockerhub-username/dev-repo' // Replace with your DockerHub dev repo
        PROD_REPO = 'your-dockerhub-username/prod-repo' // Replace with your DockerHub prod repo
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials' // ID of Docker Hub credentials in Jenkins
        GIT_BRANCH = "${env.GIT_BRANCH}" // Git branch name
    }
    stages {
        stage('Checkout Code') {
            steps {
                script {
                    // Checkout code from the relevant branch (dev or master)
                    checkout scm
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def imageName = GIT_BRANCH == 'dev' ? DEV_REPO : PROD_REPO
                    sh "docker build -t ${imageName}:latest ."
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    def imageName = GIT_BRANCH == 'dev' ? DEV_REPO : PROD_REPO
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                        sh "docker push ${imageName}:latest"
                    }
                }
            }
        }
    }
    post {
        success {
            script {
                echo "Docker image pushed successfully to ${GIT_BRANCH == 'dev' ? 'dev' : 'prod'} repository."
            }
        }
        failure {
            script {
                echo "Build or push failed!"
            }
        }
    }
}


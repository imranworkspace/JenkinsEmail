pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "imrandocker24/jenkins_email:latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/imranworkspace/JenkinsEmail'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE% ."
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push %DOCKER_IMAGE%
                    """
                }
            }
        }

        stage('Run Containers') {
            steps {
                bat "docker-compose -f docker-compose.yml up -d --force-recreate"
            }
        }

    }

    post {
        success {
            emailext(
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Good news! The build succeeded.\nCheck console: ${env.BUILD_URL}console",
                to: "imranlatur24studymaterial@gmail.com"
            )
        }
        failure {
            emailext(
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Something went wrong!\nCheck console: ${env.BUILD_URL}console",
                to: "imranlatur24studymaterial@gmail.com"
            )
        }
    }
}

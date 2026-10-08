pipeline {

    agent any

    tools {
        jdk 'JDK-25'
        maven 'Maven-3.9.16'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvnw.cmd clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                bat 'mvnw.cmd test'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t kaushal7970/springboot-k8s-demo:%BUILD_NUMBER% -t kaushal7970/springboot-k8s-demo:latest -f dockerfile .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat 'echo %DOCKER_PASSWORD%| docker login -u %DOCKER_USER% --password-stdin'
                    bat 'docker push kaushal7970/springboot-k8s-demo:%BUILD_NUMBER%'
                    bat 'docker push kaushal7970/springboot-k8s-demo:latest'
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'CI/CD Pipeline failed!'
        }
    }
}

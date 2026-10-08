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
                withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'docker --version && docker build -t kaushal7970/springboot-k8s-demo:%BUILD_NUMBER% -t kaushal7970/springboot-k8s-demo:latest -f dockerfile .' }
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
                    withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'echo %DOCKER_PASSWORD%| docker login -u %DOCKER_USER% --password-stdin' }
                    withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'docker push kaushal7970/springboot-k8s-demo:%BUILD_NUMBER%' }
                    withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'docker push kaushal7970/springboot-k8s-demo:latest' }
                }
            }
        }

        stage('Kubernetes Deploy') {
            steps {
                withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'kubectl apply -f k8s/deployment.yaml' }
                withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'kubectl apply -f k8s/service.yaml' }
                withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'kubectl set image deployment/springboot-k8s-demo springboot-k8s-demo=kaushal7970/springboot-k8s-demo:%BUILD_NUMBER%' }
                withEnv(['PATH+DOCKER=C:\\Users\\kaush\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin']) { bat 'kubectl rollout status deployment/springboot-k8s-demo' }
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


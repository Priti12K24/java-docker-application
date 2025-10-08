pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_IMAGE = 'priti12k24/java-docker-app'
        DOCKER_TAG = "latest"
        GIT_REPO = 'https://github.com/Priti12K24/java-docker-application.git'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: "${GIT_REPO}",
                    credentialsId: 'github-credentials'
            }
        }
        
        stage('Build Java Application') {
            steps {
                bat 'mvn clean compile'
                bat 'mvn package -DskipTests'
            }
            post {
                success {
                    echo 'Java application built successfully!'
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
        
        stage('Test Docker Image') {
            steps {
                script {
                    def testContainer = docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    testContainer.inside {
                        bat 'java -jar /app/app.jar'
                    }
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
            post {
                success {
                    echo "Docker image pushed to Docker Hub: ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
            cleanWs()
        }
        success {
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The Docker image has been built and pushed successfully!\n\nCheck console output at: ${env.BUILD_URL}",
                to: "korakepriti2@gmail.com"
            )
        }
        failure {
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "The pipeline failed. Please check the console output at: ${env.BUILD_URL}",
                to: "korakepriti2@gmail.com"
            )
        }
    }
}
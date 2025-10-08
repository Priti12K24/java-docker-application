pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        DOCKER_IMAGE = 'priti12k24/java-docker-app'
        DOCKER_TAG = "latest"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Priti12K24/java-docker-application.git'
            }
        }
        
        stage('Check Files') {
            steps {
                bat 'dir /s'  // List all files to verify structure
                bat 'type pom.xml'  // Show pom.xml content
            }
        }
        
        stage('Build Java Application') {
            steps {
                bat 'mvn -version'  // Check Maven installation
                bat 'mvn clean compile -X'  // Run with debug info
                bat 'mvn package -DskipTests'
            }
            post {
                success {
                    echo 'Java application built successfully!'
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
                failure {
                    echo 'Java build failed! Check Maven installation and pom.xml'
                    bat 'dir target'  // Check if target directory exists
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
        }
    }
    
    post {
        always {
            echo 'Pipeline execution finished!'
            cleanWs()
        }
        success {
            echo "SUCCESS: Pipeline completed successfully!"
        }
        failure {
            echo "FAILED: Pipeline execution failed! Check the stages above for errors."
        }
    }
}
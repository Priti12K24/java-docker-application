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
        
        stage('Build Java Application') {
            steps {
                bat 'mvn clean compile'
                bat 'mvn package -DskipTests'
            }
            post {
                success {
                    echo '✅ Java application built successfully!'
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    bat 'dir target\\*.jar'
                }
            }
        }
        
        stage('Manual Docker Instructions') {
            steps {
                echo '📝 DOCKER MANUAL STEPS (since Docker is not working on this server):'
                echo '1. Build Docker image manually:'
                echo '   docker build -t priti12k24/java-docker-app .'
                echo '2. Test Docker image:'
                echo '   docker run --rm priti12k24/java-docker-app'
                echo '3. Push to Docker Hub:'
                echo '   docker push priti12k24/java-docker-app'
                echo ''
                echo '⚠️  Docker is currently not available on this Jenkins server'
                echo '✅ But your Java application is built successfully!'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo '🎉 SUCCESS: Java application built and ready!'
            echo 'JAR file is available in target/ directory'
        }
    }
}
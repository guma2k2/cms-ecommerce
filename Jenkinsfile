pipeline {

    agent any

    tools {
        maven 'my-maven' // Ensure 'my-maven' is configured in Jenkins global tool configuration
    }

    stages {

        stage('Build with Maven') {
            steps {
                sh 'mvn --version'
                sh 'java -version'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                sh 'mvn install'
            }
        }

        stage('Deploy Spring Boot to DEV') {
            steps {
                echo 'Deploying Spring Boot to DEV environment'
                sh 'docker-compose up -d' // Adding '-d' to run containers in detached mode
            }
        }

    }
    
    post {
        always {
            echo 'Cleaning up workspace'
            cleanWs() // Cleans up the workspace after the build
        }
    }
}

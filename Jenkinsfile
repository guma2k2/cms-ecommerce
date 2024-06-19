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
                sh 'mvn clean install -DskipTests'
            }
        }


        stage('Deploy Spring Boot to DEV') {
            steps {
                echo 'Deploying Spring Boot to DEV environment'
                sh 'docker compose up -d --build' 
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

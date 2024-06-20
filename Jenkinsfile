pipeline {
    agent any

    tools {
        maven 'my-maven'
    }


    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn --version'
                sh 'java -version'
                sh 'mvn clean install -DskipTests'
            }
        }


        stage('Deploy app to DEV site') {
            steps {
                echo 'Deploying Spring Boot to DEV environment'
                sh 'docker compose up -d --build'
            }
        }

    }

    post {
        always {
            echo 'Cleaning up workspace'
            cleanWs() 
        }
    }
}

pipeline {

    agent any

    tools {
        maven 'my-maven'
    }
    environment {
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
                echo 'Deploying'
                sh 'docker-compose up'
            }
        }

    }
    post {
        // Clean after build
        always {
            cleanWs()
        }
    }
}
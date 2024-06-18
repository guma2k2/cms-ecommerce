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
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                sh 'mvn install'
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
pipeline {
    agent any

    tools {
        maven 'my-maven'
    }

    parameters {
        string(name: 'REMOTE_HOST', defaultValue: '192.168.1.128', description: 'IP address of the production')
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn --version'
                sh 'java -version'
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Packaging/Pushing image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin https://index.docker.io/v1/
                        docker build -t thuanvn2002/cmsshoppingcart-web .
                        docker push thuanvn2002/cmsshoppingcart-web
                    """
                }
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
            cleanWs() // Cleans up the workspace after the build
        }
    }
}

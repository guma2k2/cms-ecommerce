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

        stage('Packaging/Pushing imagae') {

            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {
                    sh 'docker build -t thuanvn2002/cmsshoppingcart-web .'
                    sh 'docker push thuanvn2002/cmsshoppingcart-web '
                }
            }
        }


        stage('Deploy Spring Boot to DEV') {
            steps {
                echo 'Deploying Spring Boot to DEV environment'
                sh 'docker compose up -d --build' 
            }
        }

        stage('Deploy Spring Boot to Production') {
            steps {
                sshagent(credentials: ['ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no -t -l ubuntu 192.168.1.128 << EOF
                            ls 
                            cd Documents/workspace/java-project/cms-ecommerce
                            git pull
                            mvn clean install -DskipTests
                            docker compose up -d --build
                        EOF
                    '''

                }
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
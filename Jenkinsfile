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

        stage('Deploy app to production') {
            environment {
                SSH_CREDENTIALS = credentials('ssh-key')
            }
            steps {
                script {
                    def sshCommand = { host ->
                        def sshCommandString = """
                            sshpass -p '${SSH_CREDENTIALS.password}' ssh -o StrictHostKeyChecking=no ${SSH_CREDENTIALS.username}@${host} << 'ENDSSH'
                            cd Documents/workspace/java-projects/cms-ecommerce
                            git pull
                            docker rm -f web nginx mysqldb
                            docker pull thuanvn2002/cmsshoppingcart-web
                            docker-compose up -d
                            ENDSSH
                        """
                        return sshCommandString
                    }

                    sh sshCommand(params.REMOTE_HOST)
                }
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

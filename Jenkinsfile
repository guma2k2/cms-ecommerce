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

        stage('Packaging/Pushing imagae') {

            steps {
                withDockerRegistry(credentialsId: 'dockerhub', url: 'https://index.docker.io/v1/') {
                    sh 'docker build -t thuanvn2002/cmsshoppingcart-web .'
                    sh 'docker push thuanvn2002/cmsshoppingcart-web'
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
            steps {
                environment {
                    SSH_USER = credentials('ssh-key') ? credentials('ssh-key').username : ''
                    SSH_PASSWORD = credentials('ssh-key') ? credentials('ssh-key').password : ''
                }
                script {
                    // Define SSH command block
                    sshCommand = { host ->
                        sshCommandString = """
                            sshpass -p '${SSH_PASSWORD}' ssh -o StrictHostKeyChecking=no ${SSH_USER}@${host} << 'ENDSSH'
                            cd Documents/workspace/java-projects/cms-ecommerce
                            git pull
                            docker rm -f web nginx mysqldb
                            docker pull thuanvn2002/cmsshoppingcart-web
                            docker-compose up -d
                            ENDSSH
                        """
                        return sshCommandString
                    }

                    // Execute the SSH commands on the remote host
                    sshCommand(params.REMOTE_HOST).execute()
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

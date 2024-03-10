pipeline{
    agent any
    environment{
        GITHUB_PROJECT="https://github.com/MissIshwari/FlaskTest.git"
        GITHUB_BRANCH="main"
        SSH_USER='ubuntu'
        SSH_EC2='3.10.217.246'
        DOCKER_IMAGE='misscoder21/ishwari-flask-test'
        DOCKER_CREDENTIALS='ishwari-docker-credentials'
    }
    stages{
        stage("Git checkout"){
            steps{
                git branch: env.GITHUB_BRANCH, url: env.GITHUB_PROJECT
            }
        }
        stage("Copy and Test application on EC2"){
            steps{
                script{
                    sshagent(credentials: ['ishwari-aws-ssh-credentials	']) {
                        script {
                            sh """
                                scp -o StrictHostKeyChecking=no -r * ${SSH_USER}@${SSH_EC2}:/home/ubuntu
                            
                                ssh -o StrictHostKeyChecking=no ${SSH_USER}@${SSH_EC2} '
                                    sudo apt update
                                    sudo apt install python3
                                    sudo apt install pip -y
                                    sudo pip install -r requirements.txt
                                    sudo pytest test_app.py
                                '
                            
                            """
                        }
                    }
            
                }   
            }
            
        }
    
        stage("Build Docker image and push on ECR"){
            steps{
                script{
                    docker.withRegistry('',env.DOCKER_CREDENTIALS){
                        def flaskImage=docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}",".")
                        flaskImage.push()
                    }
                }
            }
        }
    }
}

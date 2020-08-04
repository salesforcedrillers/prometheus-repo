pipeline {
    agent any
    stages {
        stage('list repo contents and working directory') {
            steps {
                script {
                    sh "ls"
                    sh "pwd"
                    sh "whoami"
                }
            }
        }
        stage('create build') {
            steps {
                script {
                    sh "./gradlew clean build"
                }
            }
        }
        stage('list the created build file') {
            steps {
                script {
                    sh "ls build/libs/"
                }
            }
        }
        stage('Remove old jar from DOCKER') {
            steps {
                script {
                    sh "sudo rm DOCKER/spring-boot-with-prometheus-0.1.0.jar"
                }
            }
        }
        stage('copy jar to DOCKER') {
            steps {
                script {
                    sh "sudo cp build/libs/spring-boot-with-prometheus-0.1.0.jar DOCKER/"
                }
            }
        }
        stage('building docker image and pushing it to dockerhub') {
            steps {
                script {
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@54.87.234.62 cd /home/ubuntu/jenkins/workspace/devops-flow-salesforcedrillers/devops-flow-pipeline/DOCKER/; ls; sudo docker build -t salesforcedrillers/devops-flow:v_${BUILD_NUMBER} ."
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@54.87.234.62 sudo docker push salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                }
            }
        }
        stage('running container') {
            steps {
                script {
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@35.175.213.50 sudo docker pull salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@35.175.213.50 sudo docker kill devops-flow"
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@35.175.213.50 sudo docker rm devops-flow"
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@35.175.213.50 sudo docker run -it --name devops-flow -p 8080:8080 -d salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /var/jenkins_home/drillers.pem ubuntu@35.175.213.50 sudo docker ps"
                }
            }
        }
    }
}


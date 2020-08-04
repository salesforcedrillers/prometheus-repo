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
                    sh "cd DOCKER; sudo docker build -t salesforcedrillers/devops-flow:v_${BUILD_NUMBER} ."
                    sh "sudo docker push salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                }
            }
        }
        stage('running container') {
            steps {
                script {
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker pull salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker kill devops-flow"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker rm devops-flow"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker run -it --name devops-flow -p 8080:8080 -d salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker ps"
                }
            }
        }
        stage('remove unused images') {
            steps {
                script {
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker images -a -q > unused_images_id"
                    try {
                        sh "ssh -i /home/ubuntu/drillers.pem ubuntu@54.80.32.242 sudo docker rmi `cat unused_images_id`"
                    } catch (err) {
                        echo err.getMessage()
                    }
                }
                echo curentBuild.result
            }
        }
    }
}


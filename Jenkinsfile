pipeline {
    agent {
        label 'aws-ec2'
    }
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
        stage('building docker image') {
            steps {
                script {
                    sh "cd DOCKER; sudo docker build -t salesforcedrillers/devops-flow:v_${BUILD_NUMBER} ."
                }
            }
        }
        stage('dockerlogin') {
         steps {
             script {
                 docker.withRegistry('b51af114-1417-4402-ab6a-4a745d4d437d') {
                 }
             }
         }
      }
      stage('dcoker push') {
          steps {
              script {
                  sh "sudo docker push salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
              }
          }
      }
        stage('running container') {
            steps {
                script {
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker pull salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker kill devops-flow"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker rm devops-flow"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker run -it --name devops-flow -p 8080:8080 -d salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker ps"
                    sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker images > unused_images_cid"
                }
            }
        }
        stage ('remove images from build server') {
            steps {
                script {
                    try {
                        sh "sudo docker images -a -q > images_cid; sudo docker rmi `cat images_cid`"
                    } catch (err) {
                        echo err.getMessage()
                    }
                }
                echo currentBuild.result
            }
        }
        stage('remove unused images') {
            steps {
                script {
                    try {
                        sh "ssh -i /home/ubuntu/drillers.pem ubuntu@34.239.254.117 sudo docker rmi `cat unused_images_cid`"
                    } catch (err) {
                        echo err.getMessage()
                    }
                }
                echo currentBuild.result
            }
        }
    }
}


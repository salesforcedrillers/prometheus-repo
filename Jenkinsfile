pipeline {
    agent {
        node {
            label 'java-node'
        }
    }
    stages {
        stage('list repo contents and working directory') {
            steps {
                script {
                    sh "ls"
                    sh "pwd"
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
                    sh "cd DOCKER; sudo docker build -t salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                    sh "sudo docker push salesforcedrillers/devops-flow:v_${BUILD_NUMBER}"
                }
            }
        }
    }
}

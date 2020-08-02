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
    }
}

pipeline {
    agent any
    tools {
        maven 'maven-3'
        jdk 'jdk-21'
    }
    environment {
        SERVICE = 'auth-service'
        NAME = "leosfreitas/${env.SERVICE}"
    }
    stages {
        stage('Dependencies') {
            steps {
                echo 'Installing dependencies...'
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }      
        stage('Build & Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USERNAME', passwordVariable: 'TOKEN')]) {
                    sh "docker login -u $USERNAME -p $TOKEN"
                    sh "docker build create --use --platform=linux/arm64,linux/amd64 --node multi-platform-builder-${env.SERVICE} --name multi-platform-builder-${env.SERVICE}"
                    sh "docker build build --platform=linux/arm64,linux/amd64 --push --tag ${env.NAME}:latest --tag ${env.NAME}:${env.BUILD_ID} -f Dockerfile ."
                    sh "docker build rm --force multi-platform-builder-${env.SERVICE}"
                }
            }
        }
    }
}
pipeline {
    agent any
    tools {
        maven 'maven-3.6.3' 
    }
    options {
        skipStagesAfterUnstable()
    }
    environment {
        COMPOSE_FILE = "docker-compose.yml"
        IMAGENAME = israelpg/product-service
        DATE = new Date().format('yy.M')
        TAG = "${DATE}.${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout SCM repo: https://github.com/israelpg/product-manage-challenge') { 
            steps { 
                script{
                    checkout scm
                }
            }
        }
        stage ('Maven Package') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Maven Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Build Image') {
            steps {
                docker.build("${IMAGENAME}:${TAG}")                           
            }
        }
        stage('Docker Start Application') {
            steps {
                sh "docker-compose up --detach"                                
            }
        }
	    stage('Pushing Docker Image to Dockerhub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_credential') {
                        docker.image("${IMAGENAME}:${TAG}").push()
                        docker.image("${IMAGENAME}:${TAG}").push("latest")
                    }
                }
            }
        }
    }
}

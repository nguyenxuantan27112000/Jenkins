def img
pipeline {
    environment {
        registry = "nguyenxuantan/repo" //To push an image to Docker Hub, you must first name your local image using your Docker Hub username and the repository name that you created through Docker Hub on the web.
        registryCredential = 'docker-hub-login'
        dockerImage = ''
    }
    
    
    agent any
    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/kss7/SimpleFlaskUI.git'
            }
        }
        
        stage ('Stop previous running container'){
            steps{
                sh returnStatus: true, script: 'docker stop $(docker ps -a | grep ${JOB_NAME} | awk \'{print $1}\')'
                sh returnStatus: true, script: 'docker rmi $(docker images | grep ${registry} | awk \'{print $3}\') --force' //this will delete all images
                sh returnStatus: true, script: 'docker rm ${JOB_NAME}'
            }
        }

        stage('Build Image') {
            steps {
                script {
                    img = registry + ":${env.BUILD_ID}"
                    println ("${img}")
                    dockerImage = docker.build("${img}")
                }
            }
        }

        stage("Test - Run Docker Container on Jenkins node") {
           steps {
                sh "docker run -d --name ${JOB_NAME} -p 4000:4000 ${img}"
            }
        }

        stage("Push To DockerHub") {
            steps {
                script {
                    docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
        

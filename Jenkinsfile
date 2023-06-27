pipeline {
    agent any
    
    tools {
        jdk "jdk11"
        maven "maven3"
    }

    environment {
        registry = "226588068670.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/naresh9919/docker-spring-boot.git']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        
        stage ("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        
        stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 226588068670.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push 226588068670.dkr.ecr.ap-south-1.amazonaws.com/my-docker-repo:$BUILD_NUMBER"
                }
            }
        }
        
        stage ("Helm Deploy") {
            steps {
                script {
                    sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}

pipeline {
    tools {
        maven 'Maven3'
    }
    agent any

    environment {
        registry = "222189480339.dkr.ecr.ap-south-1.amazonaws.com/devops/my-repo"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Ravali-218/docker-spring-boot.git']])
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
                    sh "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 222189480339.dkr.ecr.ap-south-1.amazonaws.com"
                    sh "docker push 222189480339.dkr.ecr.ap-south-1.amazonaws.com/devops/my-repo:$BUILD_NUMBER"                   
                }
            }
        }
        
        stage ("Helm Deploy") {
            steps {
                    sh "helm upgrade first --install apr25-chart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
    }
}

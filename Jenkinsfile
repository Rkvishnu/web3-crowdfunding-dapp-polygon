pipeline {
    agent {
        label 'master'
    }
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_ACCOUNT_ID = credentials('AWS_ACCOUNT_ID')
        AWS_DEFAULT_REGION = "us-east-1"
        ECR_REGISTRY=  ""
    }
    stages {
        stage('Create EKS cluster with Terraform') {
             steps {
                script {
                    dir('terraform') {
                        sh "terraform init"
                        sh "terraform apply --auto-approve"
                    }
                }
            }
           
        }
 
        stage('Build and Push Docker image to ECR') {
            steps {        
                     script {
                        dir('src')
                    {
                        sh 'docker build -t webapp:latest .'
                        sh "docker tag webapp:latest ${ECR_REGISTRY}:latest"
                        sh "docker push ${ECR_REGISTRY}:latest"
                    }
                    }
                
            }
        }
    
       stage("Deploy to EKS") {
            steps {
                script {
                    dir('kubernetes') {
                        sh "aws eks update-kubeconfig --name myapp-eks-cluster"
                        sh "kubectl apply -f deployment.yaml"
                        sh "kubectl apply -f service.yaml"
                    }
                }
            }
        }
    }
}

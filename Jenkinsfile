pipeline {
    agent { label 'worker-node' }

    environment {
        IMAGE_NAME  = "my-repo"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        ECR_REGISTRY = "883999921903.dkr.ecr.ap-southeast-1.amazonaws.com"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
            }
        }

        

        stage('Push to ECR') {
           
           steps {
                
                sh '''
                  aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin $ECR_REGISTRY

                  docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                  docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_REGISTRY/$IMAGE_NAME:latest
 
                  docker push $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                  docker push $ECR_REGISTRY/$IMAGE_NAME:latest 
                 
                 '''
                 }
            }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'echo Deploy'
            }
        }
    }

    post {

        always {
            cleanWs()
        }
    }
}
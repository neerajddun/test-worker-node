pipeline {
    agent { label 'worker-node' }

    environment {
        IMAGE_NAME  = "my-repo"
        IMAGE_TAG    = "${BUILD_NUMBER}"
        ECR_REGISTRY = "883999921903.dkr.ecr.ap-southeast-1.amazonaws.com"
        AWS_REGION  = "ap-southeast-1"
        EKS_CLUSTER = "test-cluster"
        KUBECONFIG  = "/var/jenkins_home/.kube/config"
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
                  aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY

                  docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                  docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_REGISTRY/$IMAGE_NAME:latest
 
                  docker push $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                  docker push $ECR_REGISTRY/$IMAGE_NAME:latest 
                 
                 '''
                 }
            }

        stage('Deploy to EKS') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                    sh """
                        aws eks update-kubeconfig --region ${AWS_REGION} --name ${EKS_CLUSTER} --kubeconfig ${KUBECONFIG}
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml 
                    """
                }
            }
        }
    }

    post {

        always {
            cleanWs()
        }
    }
}
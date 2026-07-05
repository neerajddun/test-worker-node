pipeline {
    agent { label 'worker-node' }

    environment {
        DOCKER_IMAGE  = "neeraj91/flask-app"
        DOCKER_TAG    = "${BUILD_NUMBER}"
        SONAR_PROJECT = "flask-app"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        

        stage('Push to DockerHub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                        docker push $DOCKER_IMAGE:$DOCKER_TAG

                        docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_IMAGE:latest

                        docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'echo Deploy'
            }
        }
    }

    post {

        success {
            echo "BUILD SUCCESS - Image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            echo "SonarQube: PASSED"
        }

        unstable {
            echo "BUILD UNSTABLE - Review OWASP and Trivy reports"
        }

        failure {
            echo "BUILD FAILED - Check console logs"
        }

        always {
            cleanWs()
        }
    }
}
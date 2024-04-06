pipeline {
    environment {
        DOCKER_ID = "kentronic"
        DOCKER_IMAGE_SHIPPING = "shipping"
        DOCKER_TAG = "${BUILD_ID}"
        BUILD_AGENT  = ""
        NAMESPACE = credentials("NAMESPACE")
    }
agent any
    stages {
        stage('Build') {
            steps { //create a loop somehow??
                sh 'docker build -t $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:$DOCKER_TAG .'
            }
        }
        stage('Run') {
            steps {
                sh 'docker network create $BUILD_TAG'
                sh 'docker run -d --name $DOCKER_IMAGE_SHIPPING --rm --network $BUILD_TAG $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:$DOCKER_TAG'
                sh 'docker stop $DOCKER_IMAGE_SHIPPING'
                sh 'docker network rm $BUILD_TAG'    
            }
        }
        stage('Push') {
            environment {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                sh 'docker image tag $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:$DOCKER_TAG $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:latest'
                sh 'docker login -u $DOCKER_ID -p $DOCKER_PASS'
                sh 'docker push $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:$DOCKER_TAG && docker push $DOCKER_ID/$DOCKER_IMAGE_SHIPPING:latest'
            }
        }    
    }
}

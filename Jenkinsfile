pipeline{
  agent any
  environment{
    REGISTRY="trialt9p4w1.jfrog.io"
    REPO="simply-flask"
    IMAGE_NAME="${REGISTRY}/${REPO}/my-python-app:latest"
  }
  stages{
    stage('Build docker image'){
      steps{
        echo 'Building Docker Image'
        sh 'docker build -t my-python-app .'
      }
  }
    stage("Push image to JFROG"){
      steps{
        echo 'Tagging and Pushing Image to JFROG'
        withCredentials([usernamePassword(credentialsId: 'jfrog-docker-creds', usernameVariable: 'USERNAME', passwordVariable:'PASSWORD')]){
          sh '''  
              echo "$PASSWORD" | docker login $REGISTRY -u "$USERNAME" --password-stdin
              docker tag my-python-app $IMAGE_NAME
              docker push $IMAGE_NAME
              docker logout $REGISTRY
          '''
         
        }
      }
    }
    stage('Deploy'){
      steps{
        echo 'pulling image from JFROG'
        withCredentials([usernamePassword(credentialsId: 'jfrog-docker-creds', usernameVariable: 'USERNAME', passwordVariable:'PASSWORD')]){

          sh '''
         
            echo "$PASSWORD" | docker login $REGISTRY -u "$USERNAME" --password-stdin
            docker pull $IMAGE_NAME
            docker run -d -p 5000:5000 $IMAGE_NAME
            docker logout $REGISTRY
          '''
        }
      }
    }
  }
} 

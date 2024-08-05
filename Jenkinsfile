pipeline {
  agent any
  environment { 
    DOCKER_ID = "antoinepela"
    DOCKER_IMAGE = "datascientestapi"
    DOCKER_TAG = "v.${BUILD_ID}.0" 
  }
  stages {
    stage('Building') {
      steps {
        sh 'echo "Installation des librairies"'
        sh 'pip install -r requirements.txt'        
      }      
    }
    // stage('Testing') {
    //   steps {
    //     sh 'echo "Tests unitaires"'
    //     sh 'python -m unittest'
    //   }      
    }
    stage('Deploying') {
      steps {
        echo 'Création image Docker'
        script {
          sh '''
          docker rm -f jenkins
          docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
          docker run -d -p 8000:8000 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
          '''
        }
      }
    }
    // stage('User Acceptance') {
    //   steps {
    //     input {
    //       message "Proceed to push to main"
    //       ok "Yes"
    //     }
    //   }
    // }
    stage('Pushing and Merging') {
      parallel {
        stage('Pushing') {
          environment {
            DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_PASS')
          }
          steps {
            sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            sh 'docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG'
          }        
        }
        stage('Merging') {
          steps {
            echo 'Merging done'
          }          
        }
      }
    }
    post {
    always {
      echo "Deconnexion de Dockerhub"
      sh "docker logout"
    }
  }    
  }
  

pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }
  environment {
        PROJECT_ID = 'round-forge-355507'
        CLUSTER_NAME = 'tk-cluster'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'mygke'
    }
    
  stages {

    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=tarunk0/myweb:${BUILD_NUMBER}
            '''
          }
        }
      }
    }
    stage('Deploy to GKE') {
            steps{
              container('kubectl'){
                echo "deployment started .."
                sh 'ls -ltr'
                sh 'pwd'
                sh 'sed -i "s/<TAG>/${BUILD_NUMBER}/" myweb.yaml'
                step([
                $class: 'KubernetesEngineBuilder',
                projectId: env.PROJECT_ID,
                clusterName: env.CLUSTER_NAME,
                location: env.LOCATION,
                manifestPattern: 'myweb.yaml',
                credentialsId: env.CREDENTIALS_ID,
                verifyDeployments: false])
                echo "congratulations! Deployment Finished..."
              }
            }
              
        }
    }
  
  }

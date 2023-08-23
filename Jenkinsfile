pipeline{
  agent any

  stages{
    stage("Delete Old Container"){
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          echo "Delete the container"
          sh "docker rm -f demo-pipeline"
        }
      }
    }
    stage('Build Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'harbor-user', usernameVariable: 'HARBOR_USER', passwordVariable: 'HARBOR_PSWD')]) {
          sh "docker login harbor.hpe-taiwan-cic.net -u=\"${HARBOR_USER}\" -p=\"${HARBOR_PSWD}\""
          sh "docker build -t demo-pipeline ."
          sh "docker tag demo-pipeline harbor.hpe-taiwan-cic.net/jenkins/demo-pipeline:1.0"
          sh "docker push harbor.hpe-taiwan-cic.net/jenkins/demo-pipeline:1.0"
          }
       }
    } 

    stage("Deploy"){
      steps{
        echo "deploy begin"
        sh "docker run -d -p 80:80 --name demo-pipeline demo-pipeline "
      }
    }

    stage("Verify"){
      steps{
        sh "curl 20.6.0.134"  
      }    
    }
  }
  
  post {
    failure {
      echo "No!! it fail"
    }
    success {
      echo "Yes!! it work"
    }
  }
}

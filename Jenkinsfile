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
          sh "docker login harbor.ymcop.com -u=\"${HARBOR_USER}\" -p=\"${HARBOR_PSWD}\""
          sh "docker build -t demo-pipeline ."
          sh "docker tag demo-pipeline harbor.ymcop/jenkins/demo-pipeline:1.0"
          sh "docker push harbor.ymcop.com/jenkins/demo-pipeline:1.0"
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
        sh "curl demo.ymcop.com"  
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

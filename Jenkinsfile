pipeline {
  environment {
    registry = "namishjain253/shopon"
    registryCredential = 'docker_hub_namishjain'
    dockerImage = ''
  }
  agent any
  tools{
    maven 'MAVEN_HOME'
    jdk 'JAVA_HOME'
  }
  stages{
    stage ('Build') {
      steps{
        echo "Building Project"
        bat ''' mvn package -DskipTests '''
      }
    }
    stage ('Archive') {
      steps{
        echo "Archiving Project"
        archiveArtifacts artifacts: '**/*.war', followSymlinks: false
      }
    }
    stage ('Build Docker Image') {
      steps{
        echo "Building Docker Image"
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage ('Push Docker Image') {
      steps{
        echo "Pushing Docker Image"
        script {
          docker.withRegistry( '', registryCredential ) {
              dockerImage.push()
              dockerImage.push('latest')
          }
        }
      }
    }
    stage ('Deploy to Dev') {
      steps{
        echo "Deploying to Dev Environment"
        bat ''' docker rm -f shopon || true 
                docker run -d --name=shopon -p 9080:9080 namishjain253/shopon '''
      }
    }
  }
}

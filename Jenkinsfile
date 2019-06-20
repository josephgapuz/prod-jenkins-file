pipeline {
  environment {
     buildArtifact = "http://localhost:9090/repository/snapshots/com/sample/sandbox/1.0-SNAPSHOT/sandbox-1.0-20190606.202852-1.war"
   }
  agent any
  stages {
    stage('Download Artifact') {
      steps {
        bat label: '', script: 'curl %buildArtifact% --output got.war'
        archiveArtifacts(artifacts: 'got.war', fingerprint: true)
      }
    }
    stage('Deploy To QA Tomcat') {
      steps {
        build job: 'GOT-Deploy-to-Test'
      }
    }   
  }
}

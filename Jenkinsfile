def COLOR_MAP = ['SUCCESS': 'good', 'FAILURE': 'danger', 'UNSTABLE': 'danger', 'ABORTED': 'danger']

pipeline 
{
  environment 
  {
     buildArtifact = "http://localhost:9090/repository/internal/com/sample/sandbox/${BUILD_VERSION}/sandbox-${BUILD_VERSION}.war"
  }
  
  agent any
  
  stages 
  {
    stage('Download Artifact') 
    {
      steps 
      {
      	slackSend channel: '#got', 
			color: COLOR_MAP['SUCCESS'],
			message: "*STARTED:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} More info at: ${env.BUILD_URL}"
			
        bat label: '', script: "curl %buildArtifact% --output sandbox-${BUILD_VERSION}.war"
        archiveArtifacts(artifacts: "sandbox-${BUILD_VERSION}.war", fingerprint: true)
      }
    }
    
    
    stage('Deploy To PROD Tomcat') 
    {
      steps 
      {
        build job: 'GOT-Deploy-to-Prod', parameters: [string(name: 'BUILD_VERSION', value: "${BUILD_VERSION}")]
      }
    } 
      
  }
  
  
  post 
  {
	always 
		{
			echo 'Sending email notification!'
				
			emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
				recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
				subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
				
				
			echo 'Sending slack notification!'
            
            slackSend channel: '#got',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} More info at: ${env.BUILD_URL}"
				
		}
	}

}

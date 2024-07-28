def sendSlackNotifications(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}


node{
   
buildName 'Dev - ${BUILD_NUMBER}'
buildDescription 'pipeline Script - Scriptedway'    
    def mavenHome = tool name: "maven3.9.8"   
    
echo "The Node name is:  ${env.NODE_NAME} "
echo "The Job name is:  ${env.JOB_NAME} "
echo "The Build number is:  ${env.BUILD_NUMBER} "   

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '10', daysToKeepStr: '', numToKeepStr: '10')), [$class: 'JobLocalConfiguration', changeReasonComment: '']])
 properties([[$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * * ')])])   
    
//Checkout Stage
stage ('CheckoutCode'){
  git branch: 'development', credentialsId: '94d3892b-ee31-4451-82ff-ffb52b7da40a', url: 'https://github.com/khushiserver/maven-web-application.git'
}

//Build Stage
stage ('build'){
sh "$mavenHome/bin/mvn clean package"
}

//Generate SonarQube Report
stage ('SonarQubeReport'){
sh "$mavenHome/bin/mvn sonar:sonar"
}

//Upload Artifact into Artifactory repo
stage ('UploadArtifactsIntoNexus'){
sh "$mavenHome/bin/mvn deploy"
}

//Deploy App into Tomcat Server
stage ('DeployAppIntoTomcat'){
sshagent(['cea917b9-393c-41e4-b019-cb45305096f3']) {
  sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ubuntu@65.0.18.36:/opt/apache-tomcat-9.0.91/webapps"
}
}
*/
}//Try block closing 
catch(e){
currentBuild.result = "FAILED"
}
finally{
sendSlackNotification(currentBuild.result)
}


}//Node Closing

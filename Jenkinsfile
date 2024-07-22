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

}//Node Closing

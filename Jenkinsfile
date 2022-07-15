node
{
 def mavenHome = tool name:"maven 3.8.5" 
 properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
echo "the jobname is: ${env.JOB_NAME}" 
echo "the job basename is: ${env.JOB_BASE_NAME}" 
echo "the build tag is: ${env.BUILD_TAG}" 
echo "the build ID is: ${env.BUILD_ID}" 
echo "the build number is: ${env.BUILD_NUMBER}" 
 try{
  slacknotifications("STARTED")
stage ('CheckoutCode'){
git branch: 'development', credentialsId: '4ed1ce52-1588-4c71-83f4-9641f9992a73', url: 'https://github.com/owezsyed/maven-web-application.git'
}

stage ('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage ('executesonarqube'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}

stage ('deployartifactsintoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}
stage ('deployintotomcat'){
sshagent(['b093bacf-0c5b-41af-ab6f-784b0f809909']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@15.206.146.233:/opt/apache-tomcat-9.0.64/webapps/"
}
} 
 }
 catch(e){
  currentBuild.result = "FAILURE"
  throw e
 }
 finally{
  slacknotifications(currentBuild.result)
 }
}//NODE CLOSING
slack notifications
def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

node
{
 def mavenHome = tool name:"maven 3.8.5" 
 properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
echo "the jobname is: ${env.JOB_NAME}" 
echo "the job basename is: ${env.JOB_BASE_NAME}" 
echo "the build tag is: ${env.BUILD_TAG}" 
echo "the build ID is: ${env.BUILD_ID}" 
echo "the build number is: ${env.BUILD_NUMBER}" 

stage ('CheckoutCode'){
git branch: 'development', credentialsId: '4ed1ce52-1588-4c71-83f4-9641f9992a73', url: 'https://github.com/owezsyed/maven-web-application.git'
}
/*
stage ('Build'){
sh "${mavenHome}/bin/mvn clean package"
}

stage ('executesonarqube'){
sh "${mavenHome}/bin/mvn sonar:sonar"
}
*/
stage ('deployartifactsintoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}
stage ('deployintotomcat'){
sshagent(['b093bacf-0c5b-41af-ab6f-784b0f809909']) {
sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.66.127.13:/opt/apache-tomcat-9.0.64/webapps/"
}
} 

}

node {
  buildName 'Dev-${BUILD_NUMBER}'
  buildDescription 'Pipeline Script - Scriptway'
  
  def mavenHome= tool name:"maven3.9.6"

  echo "The Node name is: ${env.NODE_NAME}"
  echo "The Job name is: ${env.JOB_NAME}"
  echo "The Build Number is: ${env.BUILD_NUMBER}"
  

  properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: '']])

  properties([[$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
  
  //CHECKOUT STAGE
   stage('CheckOutCode'){
   git branch: 'main', credentialsId: 'e34a898a-5df6-4ef1-a69e-6f203acb650d', url: 'https://github.com/the-learning001/maven-web-app.git'
   }

   //BUILD STAGE
   stage('Build'){
    sh "$mavenHome/bin/mvn clean package"
   }

   //SONARQUBE REPORT
   stage('SonarQubeReport'){
    sh "$mavenHome/bin/mvn sonar:sonar"
   }

   //UPLOAD THE ARTIFACT INTO ARTIFACTORY REPOSITORY
   stage('ArtifactoryRepository'){
    sh "$mavenHome/bin/mvn deploy"
   }

   //DEPLOY THE APP INTO TOMCAT SERVER
   stage('DeployIntoTomcat'){
    sshagent(['bbe4b692-c909-49be-9add-319cd05129b3']) {
      sh "scp -o StrictHostKeyChecking=no target/01-maven-web-app.war  ec2-user@13.233.227.27:/opt/tomcat/webapps"
    }
   }

}

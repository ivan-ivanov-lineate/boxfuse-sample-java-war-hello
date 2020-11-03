void init_ssh(String ssh_key)
{
    echo "Configure ssh"
    sh """
        mkdir -p ~/.ssh
        cat ${ssh_key} > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        echo "Host *" > ~/.ssh/config
        echo "    StrictHostKeyChecking no" >>  ~/.ssh/config
    """
}

pipeline {
  agent {
      label 'slave1'
  }
  stages {
    stage('Building image') {
      steps{
        script {
          sh "mvn package"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {        
        withCredentials([usernamePassword(credentialsId: 'tomcat', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASSWORD')]) {
        sh """
          CURL_RESPONSE=\$(curl -v -u $TOMCAT_USER:$TOMCAT_PASSWORD -T target/*.war "http://34.107.24.94:8080/manager/text/deploy?path=/helloworld&update=true")    
          if [[ \$CURL_RESPONSE == *"FAIL"* ]]; then
            echo "war deployment failed"
            exit 1
          else    
            echo "war deployed successfully "
            exit 0
          fi
        """
          }
        }

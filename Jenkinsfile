pipeline {
    agent any
    environment {
      TOMCAT_DEV = "172.31.52.202"
      TOMCAT_USER = "ec2-user"
   }
     parameters {
         string defaultValue: 'main', description: 'choose the branch and deploy', name: 'branchName'
     }

    stages {
        stage ('Git checkout') {
           when {
                expression {
                    params.branchName == "Dev"
                }
            }    
            steps {
                 git branch: "${params.branchName}", credentialsId: 'git-creds', url: 'https://github.com/Tangala123/package'
            }
        }
           
         stage('Maven Build') {
              when {
                expression {
                    params.branchName == "Dev"
                }
            }    
            steps {
             sh 'mvn clean package'   
            }
        }
        stage('Dev Deploy') {
             when {
                expression {
                    params.branchName == "Dev"
                }
            }    
            steps {
                sshagent(['tomcat-creds']) {
                   sh 'scp -o StrictHostKeyChecking=no target/*.war $TOMCAT_USER@$TOMCAT_DEV:/opt/tomcat9/webapps/'
                   sh 'ssh  $TOMCAT_USER@$TOMCAT_DEV /opt/tomcat9/bin/shutdown.sh'
                    sh 'ssh  $TOMCAT_USER@$TOMCAT_DEV /opt/tomcat9/bin/startup.sh'
                }
            }
        }
    }
    post {
     success {
          echo " This job should run when it success "
     }
      failure {
          echo " This job should run when it fails "
     }
   }

}

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('DeployToStaging') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'webserver_login' usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                   sshPublisher(
                       failOnError: true,
                       continueOnError: false,
                       publishers: [
                           sshPublisherDesc(
                               configName: 'staging_server',
                               sshCredentials: [
                                   username: "$USERNAME",
                                   encryptedPassphrase: "$USERPASS"
                              ],
                              transfers: [
                                  sshTransfer(
                                      sourceFiles: 'dist/trainsSchedule.zip',
                                      removePrefix: 'dist/',
                                      remoteDirectory: '/tmp',
                                      execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /t'
                                 )
                              ]
                           )
                         ]
                       )
                   }
                }
              }
       stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_login' usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                   sshPublisher(
                       failOnError: true,
                       continueOnError: false,
                       publishers: [
                           sshPublisherDesc(
                               configName: 'production_server',
                               sshCredentials: [
                                   username: "$USERNAME",
                                   encryptedPassphrase: "$USERPASS"
                              ],
                              transfers: [
                                  sshTransfer(
                                      sourceFiles: 'dist/trainsSchedule.zip',
                                      removePrefix: 'dist/',
                                      remoteDirectory: '/tmp',
                                      execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /t'
                                 )
                              ]
                           )
                         ]
                       )
                   }
                }          
             }       
          }
        } 

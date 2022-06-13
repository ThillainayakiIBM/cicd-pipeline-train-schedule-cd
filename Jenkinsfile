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
        stage('deploy to stage'){
             when {
                branch 'master'
              }
             steps{
                withCredentials([usernamepassword(credentialsId:'webserver',usernameVariable:'USERNAME',passwordVariable:'PASSWD')])
                {
                    sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$PASSWD"
                                ], 
                                transfers:[
                                    sshTransfer(
                                    sourceFiles: '/dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -f /opt/trainSchedule/* && unzip /tmp/trainSchedule.zip -d /opt/trainSchedule && sudo /usr/bin/systemctl start train-schedule'
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

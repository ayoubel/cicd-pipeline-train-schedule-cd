pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build '
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage ('deploy to Staging') {
			when  {
				branch 'master'
			}
			steps {
				withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
					sshPublisher(
						failOnError: true,
						ContinueOnError: false,
						publishers: [
							sshPublisherDesc(
								configName: 'staging',
								sshCredentials: [username: "$USERNAME", encryptedPassPhrase: "$PASSWORD"],
								transfers: [
									sshTransfer(
										sourceFiles: 'dist/trainSchedule.zip',
										removePrefix: 'dist/',
										remoteDirectory: '/tmp',
										execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm-rf /opt/train-schedule/* && unzip /tmp/train-schedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
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

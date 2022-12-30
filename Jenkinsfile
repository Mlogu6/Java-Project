pipeline {
    agent any 
    stages {
        stage("sonar quality check"){
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'Sonar-Token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
		    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
<<<<<<< HEAD
                    }

                }
            }
	   }
    }
}
=======
                   }
               }
            }
	}
    }
}
>>>>>>> 5bedc0b9a31ade9a7793a7c7379ddec49b17b8df

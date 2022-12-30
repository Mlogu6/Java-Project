pipeline {
    agent any 
    stages {
        stage("sonar quality check"){
<<<<<<< HEAD
            steps{
                script{
=======
            steps {
                script {
>>>>>>> e7b2e9bb87ee10e2c73dd222dfaf08c0db9c8da9
                    withSonarQubeEnv(credentialsId: 'Sonar-Token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
		    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
<<<<<<< HEAD

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
>>>>>>> e7b2e9bb87ee10e2c73dd222dfaf08c0db9c8da9

pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Jenkins-Token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }

                }
            }
	}
    }
}

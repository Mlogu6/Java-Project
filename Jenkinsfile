pipeline {
    agent any 
    stages {
        stage("sonar quality check"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'Sonar-Token') {
                            sh 'chmod +x gradlew'
                            sh './gradlew sonarqube'
                    }
                }
            }
	}
    }
}

pipeline {
    agent any
    enviroment{
        VERSION = "${env.BUILD_ID}"
    }
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
                    }

                }
            }
	   }
       stage("docker build & docker push") {
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                            sh '''
                                docker build -t 13.234.117.72:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 13.234.117.72:8083
                                docker push 13.234.117.72:8083/springapp:${VERSION}
                                docker rmi 13.234.117.72:8083/springapp:${VERSION}
                            '''
                    }
                    
                }
            }
       }
    }
}
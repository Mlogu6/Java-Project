pipeline {
    agent any
    environment{
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
                                docker build -t 3.110.131.118:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 3.110.131.118:8083
                                docker push 3.110.131.118:8083/springapp:${VERSION}
                                docker rmi 3.110.131.118:8083/springapp:${VERSION}
                              '''
                    }
                }
            }
        }
        stage('datree configuration') {
            steps{
                script{
                    dir('kubernetes/') {
                        withEnv(['DATREE_TOKEN=3905f8bf-5fff-4276-a2f3-840795d1a718']) {
                            sh 'helm datree test myapp/'
                            }
                        }
                    }
                }
            }
        }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "mlogu6@gmail.com";  
	}
}
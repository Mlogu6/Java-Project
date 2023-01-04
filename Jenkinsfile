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
                                docker build -t 13.126.66.193:8083/springapp:${VERSION} .
                                docker login -u admin -p $docker_password 13.126.66.193:8083
                                docker push 13.126.66.193:8083/springapp:${VERSION}
                                docker rmi 13.126.66.193:8083/springapp:${VERSION}
                              '''
                    }
                }
            }
        }
        stage('datree configuration') {
            steps{
                script{
                    dir('kubernetes/') {
                        withEnv(['DATREE_TOKEN=876f7fc5-06d9-4512-b454-55622b24609b']) {
                            sh 'helm datree test myapp/'
                            }
                        }
                    }
                }
            }
        stage("Pushing Helm chart to Nexus with Versioning") {
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                            dir('kubernetes/') {
                            sh '''
                                helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ' )
                                tar -czvf myapp-${helmversion}.tgz myapp/
                                curl -u admin:$docker_password http://13.126.66.193:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                              '''
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
}
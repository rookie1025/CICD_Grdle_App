pipeline{
    agent any

    environment{

        VERSION = "${env.BUILD_ID}"
    }

    stages{

        stage("Sonar quality check"){
            // agent {
            //     docker {
            //         image 'openjdk:11'
            //     }
            // }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }

                    timeout(5) {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
            }
        }

        stage("docker build & docker push"){

            steps{

                script{ 
                        withCredentials([string(credentialsId: 'docker_pass', variable: 'DOCKER_PASS')]) {
                        sh  '''
                            docker build -t 172.31.34.167:8083/springapp:${VERSION} .
                            echo "$DOCKER_PASS" | docker login -u admin -p 1234 172.31.34.167:8083
                            docker push 172.31.34.167:8083/springapp:${VERSION}
                            docker rmi 172.31.34.167:8083/springapp:${VERSION}
                        '''
                     }    
                }
            }
        }

        stage("upload helm charts to repo"){

            steps{

                script{
                    
                    withCredentials([string(credentialsId: 'NEXUS_PASS', variable: 'nexus_pass')]) {
                    dir('kubernetes/') {
                        sh  '''
                             
                             helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                             tar -czvf myapp-${helmversion}.tgz myapp/
                             curl -u admin:$nexus_pass http://172.31.34.167:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v
                        ''' 
                    }
                    }
                            
                }
            }
        }

    }

    post {

		    always {

			// mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "awstemporary0@gmail.com";  
		    sh ''' 
                echo "hello done"
            '''
            }
	    }
        
}


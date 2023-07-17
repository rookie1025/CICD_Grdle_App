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
        
    }
}

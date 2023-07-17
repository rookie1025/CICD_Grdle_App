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
                            docker build -t 13.126.113.51:8083/springapp:${VERSION} .
                            echo "$DOCKER_PASS" | docker login -u admin -p 1234 172.31.34.167:8083
                            docker push 13.126.113.51:8083/springapp:${VERSION}
                            docker rmi 13.126.113.51:8083/springapp:${VERSION}
                        '''
                    }
                }
            }
        }
        
    }
}

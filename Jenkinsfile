pipeline{
    agent any
    stages{
        stage("Sonar quality check"){
            agent {
                docker {
                    sh 'sudo chmode 777 /var/run/docker.sock'
                    image 'openjdk:11'
                }
            }
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
                    }
                }
            }
        }
        
    }
}

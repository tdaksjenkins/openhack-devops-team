pipeline {

    agent any
    tools {
        maven 'mvn'
    }
    stages {
        stage('initial'){
            steps{
                script{
                    ACR_URL = 'openhack58u7acr.azurecr.io'
                    DATE = new Date().format("YYYY-MM-dd-HH:mm:ss", TimeZone.getTimeZone('America/Toronto'))
                    echo ACR_URL
                    echo DATE
                }
            }
        }
        stage('unit test: user-java'){
            steps{
                script{
                    withMaven(                   
                        maven: 'mvn',
                    ) {                
                        sh 'mvn -f apis/user-java/pom.xml test'
                    } 

                }
            }
        }
        stage('Building image') {
            steps{
                script {
                        sh 'docker build "apis/user-java/" -t user-java:$DATE'
            }
        }
        }
        stage('Pushing image to ACR') {
            when { branch "master" } 
            steps{
                    script {
                        
                            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'ACR_JENKINS',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {                            
                                        sh 'docker login openhack58u7acr.azurecr.io'
                                        sh 'docker tag user-java:$DATE openhack58u7acr.azurecr.io/user-java:$DATE'
                                        sh 'docker push openhack58u7acr.azurecr.io/user-java:$DATE'
                               }
                       
                       
                        
                    }     
            }
        }
    }
      
    
  
}

def ACR_URL
def DATE
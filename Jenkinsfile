pipeline {

    agent any
    tools {
        maven 'mvn'
    }
    stages {
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
                        sh 'docker build "apis/user-java/" -t user-java:$BUILD_NUMBER'
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
                                        sh 'docker tag user-java:$BUILD_NUMBER openhack58u7acr.azurecr.io/user-java:$BUILD_NUMBER'
                                        sh 'docker push openhack58u7acr.azurecr.io/user-java:$BUILD_NUMBER'
                               }
                       
                       
                        
                    }     
            }
        }
    }
      
    
  
}

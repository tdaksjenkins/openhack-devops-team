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
                    DATE = new Date().format("YYYYMMddHHmmss", TimeZone.getTimeZone('America/Toronto'))
                    USER_JAVA_NAME = 'user-java'
                    USER_PROFILE = 'userprofile'
                    echo ACR_URL 
                    echo DATE
                    echo USER_JAVA_NAME
                    echo USER_PROFILE
                    

                    
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
        stage('unit test: userprofile'){
            steps{
                script{
                    nodejs(nodeJSInstallationName: 'node8') {
                        sh 'npm install --prefix apis/userprofile'
                        sh 'npm run test --prefix apis/userprofile'
                    }

                }
            }
        }
        stage('Building image: user-java') {
            steps{
                script {   
                    def cmd = "docker build apis/${USER_JAVA_NAME}/ -t ${USER_JAVA_NAME}:${DATE}"
                    echo cmd
                    sh cmd
                }
            }
        }
        stage('Building image: userprofile') {
            steps{
                script {   
                    def cmd = "docker build apis/${USER_PROFILE}/ -t ${USER_PROFILE}:${DATE}"
                    echo cmd
                    sh cmd
                }
            }
        }
        stage('Pushing image to ACR: user-java') {
            when { branch "master" } 
            steps{
                    script {
                        
                            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'ACR_JENKINS',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {                            
                                    def docker_login_cmd = "docker login ${ACR_URL}" 
                                    def docker_tag_cmd = "docker tag ${USER_JAVA_NAME}:${DATE} ${ACR_URL}/${USER_JAVA_NAME}:${DATE}"
                                    def docker_push_cmd = "docker push ${ACR_URL}/${USER_JAVA_NAME}:${DATE}"
                                    echo docker_login_cmd
                                    echo docker_tag_cmd
                                    echo docker_push_cmd
                                    sh docker_login_cmd
                                    sh docker_tag_cmd
                                    sh docker_push_cmd
                               }
                       
                       
                        
                    }     
            }
        }
        stage('Pushing image to ACR: userprofile') {
            when { branch "master" } 
            steps{
                    script {
                        
                            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'ACR_JENKINS',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {                            
                                    def docker_login_cmd = "docker login ${ACR_URL}" 
                                    def docker_tag_cmd = "docker tag ${USER_PROFILE}:${DATE} ${ACR_URL}/${USER_PROFILE}:${DATE}"
                                    def docker_push_cmd = "docker push ${ACR_URL}/${USER_PROFILE}:${DATE}"
                                    echo docker_login_cmd
                                    echo docker_tag_cmd
                                    echo docker_push_cmd
                                    sh docker_login_cmd
                                    sh docker_tag_cmd
                                    sh docker_push_cmd
                               }
                       
                       
                        
                    }     
            }
        }
    }
      
    
  
}

def ACR_URL
def DATE
def USER_JAVA_NAME
def USER_PROFILE
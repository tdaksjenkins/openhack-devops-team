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
                    GIT_ISSUE_API = 'https://api.github.com/repos/tdaksjenkins/openhack-devops-team/issues'
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
        stage('Deploy image to AKS: user-java'){
            when { branch "master" } 
            steps{
                    script {
                        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'ACR_JENKINS',
                            usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {                            
                                sh 'helm init --client-only'
                                cmd ="helm upgrade api-user-java apis/user-java/helm --set-string repository.tag=${DATE}"
                                echo cmd
                                sh cmd 
                                // def desired = sh(script: 'kubectl get deploy |grep -i ^user-java | awk -F " " \'{print $2}\'', returnStdout: true)
                                // echo desired
                                // def avaiable = sh(script: 'kubectl get deploy |grep -i ^user-java | awk -F " " \'{print $5}\'', returnStdout: true)
                                // echo avaiable 
                                
                            }
   
                    }     
            }
        }
        stage("confirm roll back"){
            when { branch "master" }
            steps{
                script{
                    MESSAGE_ROLL_BACK = "Do you want to roll back to the previous version?"
                    echo MESSAGE_ROLL_BACK

                    FLAG = input(
                        id: 'confirm', message: 'Please confirm input values', parameters: [
                        [$class: 'ChoiceParameterDefinition', choices: 'no\nyes', description: MESSAGE_ROLL_BACK, name: 'flag'],

                    ])
                    echo ("flag: "+ FLAG)
                    if(FLAG =='no'){
                        echo "skip roll back"
                    }
                    else{
                        echo 'start roll back'
                        sh 'helm rollback api-user-java 0'
                        echo 'finish roll back'
                    }
                }
                
            }
        }

    }
      
    post{
        failure {           
            script{
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'GIT-ISSUE-TOKEN',
                               usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN']]) { 
                                    def title = "bug-${JOB_NAME}-${BUILD_NUMBER}"
                                    def body = "Fix The bug"  
                                    def cmd = "curl -X POST -u ${GIT_USER}:${GIT_TOKEN} ${GIT_ISSUE_API} --data '{\"title\": \"${title}\", \"body\":\"${body}\"}'"
                                    echo cmd
                                    sh cmd                         
                                    //curl -X POST -u "allen1990zuo":"0910b6e6d5c64e3624d4ec056fdbd257a71ad49a" https://api.github.com/repos/tdaksjenkins/openhack-devops-team/issues --data '{"title":"BUG", "body":"problem"}'
                               }

                
            }           
        }
    }
  
}

def ACR_URL
def DATE
def USER_JAVA_NAME
def USER_PROFILE
def GIT_ISSUE_API
def MESSAGE_ROLL_BACK
def FLAG
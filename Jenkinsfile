pipeline {
    
    agent {
        label "linuxbuildnode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/vimallinuxworld13/jenkins-docker-maven-java-webapp.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  eshwa29/javaweb:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }
        
        
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_PAASWD', variable: 'DOCKER_HUB_PASSWORD')]) {
    // some block
                 sh "sudo docker login -u eshwar29 -p $DOCKER_HUB_PASSWORD"
}
               
               sh "sudo docker push eshwar29/javaweb:${BUILD_TAG}"
            }
            
        }
        
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
                sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   eshwar29/javaweb:${BUILD_TAG}"
                //sh 'whoami'
            }
            
        }
        
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@43.205.139.231 sudo docker rm -f myjavaapp"
                    sh "ssh ec2-user@43.205.139.231 sudo docker run  -d  -p  8080:8080 --name myjavaapp   eshwar29/javaweb:${BUILD_TAG}"
                }

            }
            
        }
        
        
         stage('QAT Test') {
            steps {
                
               // sh 'curl --silent http://43.205.139.231:8080/java-web-app/ |  grep India'
                
                retry(10) {
                    sh 'curl --silent http://43.205.139.231:8080/java-web-app/ |  grep India'
                }
            
               
            }
        }
          
        
         
         
        stage('approved') {
            steps {
                
            
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }
            
                
            }
        }
        }
        
        
         
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               
               sshagent(['QA_SSH_CRED']) {
    
                    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@15.206.173.8 sudo kubectl  delete    deployment myjavawebapp"
                    sh "ssh  ec2-user@15.206.173.8 sudo kubectl  create    deployment myjavawebapp  --image=eshwar29/javaweb:${BUILD_TAG}"
                    sh "ssh ec2-user@15.206.173.8 sudo wget https://raw.githubusercontent.com/vimallinuxworld13/jenkins-docker-maven-java-webapp/master/webappsvc.yml"
                    sh "ssh ec2-user@15.206.173.8 sudo kubectl  apply -f webappsvc.yml"
                    sh "ssh ec2-user@15.206.173.8 sudo kubectl  scale deployment myjavawebapp --replicas=5"
                }

            }
            
        } 
        
    
        
    }
    
  

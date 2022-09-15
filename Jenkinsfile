pipeline{
    agent any
    stages {
        stage('Checkout Source') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Team-26/To-Do-list.git']]])
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t jarvis99/nodeapp:latest .'
                }
            }
        }
        
        stage('Deploy Docker Image') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'dockerhublog', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u jarvis99 -p ${dockerhubpwd}'
                 }  
                 sh 'docker push jarvis99/nodeapp:latest'
                }
            }
        }
        
        stage('Deploy App on k8s') {
            steps {
                sshagent(['K8s']) {
                    sh "scp -o StrictHostKeyChecking=no deploymentservice.yml ec2-user@52.201.223.223:/home/ec2-user"
                    script {
                        try{
                           sh "ssh ec2-user@52.201.223.223 kubectl apply -f ."
                        }catch(error){
                           sh "ssh ec2-user@52.201.223.223 kubectl create -f ."
                        }
                    }
                }
            }
       }
    }
        

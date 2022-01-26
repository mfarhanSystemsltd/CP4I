pipeline {
    agent any
    stages {
        stage('Clean Workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Git checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/mfarhanSystemsltd/ACE-HelloWorld.git'
            }
        }
        stage ('Docker build image'){
            steps{
                 //For AWS ECR:
                sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 736505676141.dkr.ecr.us-east-2.amazonaws.com'
                sh 'docker build -t 736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER} .'
                //For local repo:
                sh 'docker build -t 20.54.72.51:443/ace-test:${BUILD_NUMBER} .'
            }
        }
        stage ('Push to AWS ECR'){
            steps{
                sh 'docker push 736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER}'
            }
        }
        stage ('Push to Local Image Registry'){
            steps{
                sh 'docker push 20.54.72.51:443/ace-test:${BUILD_NUMBER}'
            }
        }
        // stage ('Deploy to k8'){
        //     steps{
        //         sshagent(['jenkins-container-ssh']) {
                    
        //             sh 'scp -o StrictHostKeyChecking=no ${WORKSPACE}/deployment.yaml systemsltd@20.74.201.20:/home/systemsltd/ACE-HelloWorld/'
        //             //For AWS ECR:
        //             sh 'ssh systemsltd@20.74.201.20 sed -i "s,IMAGE_NAME,736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER}," /home/systemsltd/ACE-HelloWorld/deployment.yaml'
        //             //For local repo:
        //             //sh 'ssh systemsltd@20.74.201.20 sed -i "s,IMAGE_NAME,20.54.72.51:443/ace-test:${BUILD_NUMBER}," /home/systemsltd/ACE-HelloWorld/deployment.yaml'
        //             sh 'ssh systemsltd@20.74.201.20 kubectl apply -f /home/systemsltd/ACE-HelloWorld/deployment.yaml'
        //         }
        //     }
        // }
           stage ('Pull Helm chart from AWS ECR'){
               steps{
                   sshagent(['jenkins-container-ssh']) {
                       sh 'scp -o StrictHostKeyChecking=no ${WORKSPACE}/helm.sh systemsltd@20.74.201.20:/home/systemsltd/Helm/ECR/'
                       sh 'ssh systemsltd@20.74.201.20 chmod 700 /home/systemsltd/Helm/ECR/helm.sh'
                       sh 'ssh systemsltd@20.74.201.20 /home/systemsltd/Helm/ECR/helm.sh'
                   }
               }
           }
        stage ('Deploy via Helm'){
            steps{
                sshagent(['jenkins-container-ssh']) {
                    sh 'ssh systemsltd@20.74.201.20 helm upgrade ace-chart /home/systemsltd/Helm/ECR/ace-deployment/ --set replicaCount=3 --set image.name=736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER}'
                    // script{
                    //     try{
                    //         sh 'ssh systemsltd@20.74.201.20 cd /home/systemsltd/Helm && helm install ace-chart-1 ace-deployment/ --set replicaCount=3 --set image.name=736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER}'
                    //     }catch (Exception e){
                    //         sh 'ssh systemsltd@20.74.201.20 cd /home/systemsltd/Helm && helm install ace-chart-1 ace-deployment/ --set replicaCount=3 --set image.name=736505676141.dkr.ecr.us-east-2.amazonaws.com/ace-test:${BUILD_NUMBER}'
                    //     }
                    // }
                }
            }
        }
    }
}

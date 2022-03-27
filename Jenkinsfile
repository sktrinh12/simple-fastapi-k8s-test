pipeline {
   options {
       ansiColor('xterm')
    }
   environment {
        reigstry = "sktrinh12/fastapi-k8s"
   agent {
       dockerfile { dockerfile true }
   }
   stages {
       stage('Checkout') {
           steps {
            checkout scm
           }
       }
       stage('Publish') {
           environment {
               registryCredential = 'dockerhub'
           }
           steps{
               script {
                   def appimage = docker.build registry + ":$BUILD_NUMBER"
                   docker.withRegistry( '', registryCredential ) {
                       appimage.push("$BUILD_NUMBER")
                   }
               }
           }
       }
       stage ('Deploy') {
           steps {
               script{
                   def image_id = registry + ":$BUILD_NUMBER"
                   sh 'cat /var/jenkins_home/ansible/playbooks/fastapi-k8s-deploy.yaml'
                   sh 'sed -i "s/ID/$image_id/g" /var/jenkins_home/ansible/playbooks/fastapi-k8s-deploy.yaml'
                   sh 'cat /var/jenkins_home/ansible/playbooks/fastapi-k8s-deploy.yaml'
                   sh "ansible-playbook /var/jenkins_home/ansible/playbooks/deploy-k8s-pb.yaml"
               }
           }
       }
   }
}

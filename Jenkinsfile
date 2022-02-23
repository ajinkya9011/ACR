pipeline {
  agent any
    tools {
      maven 'maven'
                 jdk 'jdk'
    }
    stages {      
        stage('Build maven ') {
            steps { 
                    sh 'pwd'      
                    sh 'mvn  clean install package'
            }
        }
        
        stage('Copy Artifact') {
           steps { 
                   sh 'pwd'
		   sh 'cp -r target/*.jar docker'
           }
        }
         
        stage('Build docker image') {
           steps {
               script {         
                 def customImage = docker.build('initsixcloud/petclinic', "./docker")
                 docker.withRegistry('https://acr8983.azurecr.io', 'ACR_ID') {
                 customImage.push("${env.BUILD_NUMBER}")
                 }                     
        }
		}
		}
        
        stage("SSH Into k8s Server") {
			def remote = [:]
			remote.name = 'K8S master'
			remote.host = '13.70.7.113'
			remote.user = 'jenkins'
			remote.password = 'Ajinkya@8983A'
			remote.allowAnyHosts = true

            stage('Put k8s-deployment.yaml onto AKS') {
				sshPut remote: remote, from: 'k8s-deployment.yaml', into: '.'
        }

			stage('Deploy spring boot') {
				sshCommand remote: remote, command: "kubectl apply -f k8s-deployment.yaml"
        }
    }

   }
}

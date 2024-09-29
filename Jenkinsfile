pipeline {
    agent {
        label "My-Jenkins-Agent"
    }

    tools{
        jdk 'JDK21'
        maven 'Maven3'
    }

    stages {
        stage('Cleanup Workspace') {
            steps {
               cleanWs()
            }
        }
         stage('Checkout from SCM') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mehmetistanbullu/devops-003-pipeline-aws']])
            }
        }
        stage('Build Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }
         stage("SonarQube Analysis"){
           steps {
	        script {
			withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
				sh "mvn sonar:sonar"
		        }
	        }
           }
       }
	stage("Quality Gate"){
           steps {
	           script {
			   waitForQualityGate abortPipeline:false, credantialsId: 'jenkins-sonarqube-token'
		        }
	           }
           }
       }

       //  stage('Docker Image to DockerHub') {
       //      steps {
       //          script{
       //              withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {

       //                  //  sh 'echo docker login -u mimaraslan -p DOCKERHUB_TOKEN'
       //                  // bat 'echo docker login -u mimaraslan -p DOCKERHUB_TOKEN'

       //                  // sh 'echo docker login -u mimaraslan -p ${dockerhub}'
       //                    bat 'echo docker login -u mimaraslan -p ${dockerhub}'

       //                  // sh 'docker image push  mimaraslan/my-application:latest'
       //                     bat 'docker image push  mimaraslan/my-application:latest'
       //              }
       //          }
       //      }
       //  }

       //  stage('Deploy to Kubernetes'){
       //      steps{
       //          kubernetesDeploy (configs: 'deployment-service.yml', kubeconfigId: 'kubernetes')
       //      }
       //  }


       // stage('Docker Image to Clean') {
       //     steps {
       //         //   sh 'docker rmi mimaraslan/my-application:latest'
       //         //  bat 'docker rmi mimaraslan/my-application:latest'

       //         // sh 'docker image prune -f'
       //          bat 'docker image prune -f'
       //     }
       // }


    }
}

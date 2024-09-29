pipeline {
    agent {
        label 'My-Jenkins-Agent'
    }

    environment {
        APP_NAME = 'devops-003-pipeline-aws'
        RELEASE = '1.0'
        DOCKER_USER = 'istanbullumem'
        DOCKER_LOGIN = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}.${BUILD_NUMBER}"
        asd = "${IMAGE_NAME}:${IMAGE_TAG}"
    }
    tools {
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
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh 'mvn sonar:sonar'
                    }
                }
            }
        }

        // stage('Quality Gate') {
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline:false, credantialsId: 'jenkins-sonarqube-token'
        //         }
        //     }
        // }

        stage('Build & Push Docker Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_LOGIN') {
                        docker_image = docker.build "${IMAGE_NAME}:${IMAGE_TAG}"
                    }

                    docker.withRegistry('https://registry.hub.docker.com', 'DOCKER_LOGIN') {
                        docker_image = docker.push "${IMAGE_TAG}"
                        docker_image = docker.push "latest"
                    }

                // sh 'echo docker login -u mimaraslan -p DOCKERHUB_TOKEN'
                // sh 'echo docker login -u mimaraslan -p ${dockerhub}'
                // sh 'docker image push  mimaraslan/my-application:latest'
                }
            }
        }

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

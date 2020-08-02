#!/usr/bin/env groovy

properties([
	parameters([
        string(defaultValue: "master", description: 'Which Git Branch to clone?', name: 'GIT_BRANCH'),
        string(defaultValue: "parse-server-example", description: 'Which Git Repo to clone?', name: 'GIT_APP_REPO'),
        string(defaultValue: "viksharma001", description: 'Which docker account?', name: 'ACCOUNT'),
        string(defaultValue: "parse-server", description: 'Which name of the docker image?', name: 'IMAGE_NAME'),
        string(defaultValue: "v1", description: 'Which image tag?', name: 'IMAGE_TAG'),
        string(defaultValue: "/Users/vikasdeepsharma/.kube/config", description: 'Which path of ~/.kube/config', name: 'CONFIGPATH'),
        string(defaultValue: "parse-server-namespace", description: 'Namepsace for parse-server resources', name: 'NAMESPACE'),
        string(defaultValue: "parse-server", description: 'Root passphrase of DCTs', name: 'DOCKER_CONTENT_TRUST_ROOT_PASSPHRASE'),
        string(defaultValue: "parse-server", description: 'Repository passphrase of DCTs', name: 'DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE'),

	])
])

registry = "${ACCOUNT}/parse-server"
registryCredential = 'dockerhub'

stage('Clone Repo'){
    node('master'){
      cleanWs()
      checkout([$class: 'GitSCM', branches: [[name: '$GIT_BRANCH']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/vikas-prabhakar/${GIT_APP_REPO}.git']]])
    }
  }
  

stage ('Package') {
    node('master'){
finalImage = docker.build("${registry}:${IMAGE_TAG}",'.')   

        }
    
}
  stage ('Push to Dockerhub') {

         withEnv(['DOCKER_CONTENT_TRUST=1','DOCKER_CONTENT_TRUST_ROOT_PASSPHRASE=$DOCKER_CONTENT_TRUST_ROOT_PASSPHRASE','DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE=$DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE']){
          docker.withRegistry('',registryCredential) {
            finalImage.push()
          }
      }
   } 


    stage('Remove Unused docker image') {
        node('master'){
        sh "docker rmi $registry:$IMAGE_TAG"
        }
    }

stage('Deploy App') {
    node('master'){
        withEnv(["KUBECONFIG=$CONFIGPATH"]){
          sh "helm upgrade mongo-db ./helm-chart/mongo-db  --install  --namespace=$NAMESPACE"
          sh "helm upgrade parse-server ./helm-chart/parse-server --install --namespace=$NAMESPACE"
        }

        }
}

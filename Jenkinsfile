pipeline {
    agent any
    tools{
        maven 'maven_3_5_0'
    }
    stages{
        stage('Build Maven'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/anoopkoomullil/devops-automation']])
                sh 'mvn clean install'
                //cmd_failsafe('mvn clean install')
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t anoopkoomullil/devops-integration .'
                    //cmd_failsafe('docker build -t anoopkoomullil/devops-integration .')
                }
            }
        }
        stage('Push image to Hub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                   sh 'docker login -u anoopkoomullil -p ${dockerhubpwd}'
                   //cmd_failsafe('docker login -u anoopkoomullil -p ${dockerhubpwd}')
                }
                   sh 'docker push anoopkoomullil/devops-integration'
                   //cmd_failsafe('docker push anoopkoomullil/devops-integration')
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
def cmd(command) {
    if (isUnix()) { sh "${command}" } else { bat "chcp 65001\n${command}"}
}
def cmd_failsafe(command) {
    if (isUnix()) { sh "${command}" } else { bat (script: "chcp 65001\n${command}",  returnStatus: true)}
}

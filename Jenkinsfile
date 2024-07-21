pipeline {
  agent any
  environment {
        APP_NAME="phonebook"
        APP_REPO_NAME="clarusway-repo/${APP_NAME}-app"
        AWS_ACCOUNT_ID=sh(script:'aws sts get-caller-identity --query Account --output text', returnStdout:true).trim()
        AWS_REGION="us-east-1"
        ECR_REGISTRY="${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }
  stages {
    stage('Create ECR Repo') {
      steps {
        echo "Creating ECR Repo for ${APP_NAME} app"
        sh '''
        aws ecr describe-repositories --region ${AWS_REGION} --repository-name ${APP_REPO_NAME} || \
            aws ecr create-repository \
            --repository-name ${APP_REPO_NAME} \
            --image-scanning-configuration scanOnPush=true \
            --image-tag-mutability MUTABLE \
            --region ${AWS_REGION}
         '''
        }
      }
    stage('Build App Docker Images') {
      steps {
        echo 'Building App Dev Images'
        sh "docker build -t ${ECR_REGISTRY}/${APP_REPO_NAME}:web-b${BUILD_NUMBER} ${WORKSPACE}/images/image_for_web_server"
        sh "docker build -t ${ECR_REGISTRY}/${APP_REPO_NAME}:result-b${BUILD_NUMBER} ${WORKSPACE}/images/image_for_result_server"
        sh 'docker image ls'
        }
      }
    stage('Integrate Remote k8s with Jenkins') {
      steps {
        withKubeCredentials(
          kubectlCredentials: [
            [caCertificate: '', clusterName: 'cluster-1', contextName: 'kubernetes-admin@kubernetest', credentialsId: 'kube_token', namespace: 'default', serverUrl: 'https://172.31.39.56:6443']
          ]
        ) {
          sh 'ls'
          sh 'kubectl get nodes'
          sh 'helm ls'
        }
      }
    }
  }
}
pipeline {
  agent any
  
  stages {
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
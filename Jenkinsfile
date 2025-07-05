pipeline {
    agent {
        label 'slave'
    }

    stages {
        stage('Deploy To Kubernetes manifest files') {
            steps {
                   withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'csi-aks-microservices', contextName: '', credentialsId: 'k8s-token', namespace: 'microservices', serverUrl: 'https://csi-aks-microservices-dns-x9c1s2k4.hcp.centralindia.azmk8s.io']]) {
                    sh "kubectl apply -f k8sdeployment-service.yml"
                    }
            }
        }
        
        stage('Verify Deployments') {
            steps {
               withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'csi-aks-microservices', contextName: '', credentialsId: 'k8s-token', namespace: 'microservices', serverUrl: 'https://csi-aks-microservices-dns-x9c1s2k4.hcp.centralindia.azmk8s.io']]) {
                }
            }
        }
    }
}

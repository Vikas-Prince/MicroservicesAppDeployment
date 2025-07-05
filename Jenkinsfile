pipeline {
    agent {
        label 'slave'
    }

    stages {
        stage('Deploy To Kubernetes manifest files') {
            steps {
                withKubeCredentials(kubectlCredentials: [[
                    credentialsId: 'k8s-token',
                    clusterName: 'csi-aks-microservices',
                    namespace: 'microservices',
                    serverUrl: 'https://csi-aks-microservices-dns-x9c1s2k4.hcp.centralindia.azmk8s.io'
                ]]) {
                    sh "kubectl apply -f k8sdeployment-service.yml"
                }
            }
        }
        
        stage('Verify Pods') {
            steps {
                withKubeCredentials(kubectlCredentials: [[
                    credentialsId: 'k8s-token',
                    clusterName: 'csi-aks-microservices',
                    namespace: 'microservices',
                    serverUrl: 'https://csi-aks-microservices-dns-x9c1s2k4.hcp.centralindia.azmk8s.io'
                ]]) {
                    sh "kubectl get pods -n microservices"
                }
            }
        }

        stage('Verify Services') {
            steps {
                withKubeCredentials(kubectlCredentials: [[
                    credentialsId: 'k8s-token',
                    clusterName: 'csi-aks-microservices',
                    namespace: 'microservices',
                    serverUrl: 'https://csi-aks-microservices-dns-x9c1s2k4.hcp.centralindia.azmk8s.io'
                ]]) {
                    sh "kubectl get svc -n microservices"
                }
            }
        }
    }
}

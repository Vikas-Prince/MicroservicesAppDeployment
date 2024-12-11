pipeline {
    agent {
        label 'slave'
    }

    stages {
        stage('Deploy To Kubernetes manifest files') {
            steps {
                   withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-microservices', contextName: '', credentialsId: 'k8s-token', namespace: 'microservices', serverUrl: 'https://CE5FEB5F965E1A06424B71277BDD85E3.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl apply -f k8sdeployment-service.yml"
                    }
            }
        }
        
        stage('Verify Deployments') {
            steps {
               withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-microservices', contextName: '', credentialsId: 'k8s-token', namespace: 'microservices', serverUrl: 'https://CE5FEB5F965E1A06424B71277BDD85E3.gr7.ap-south-1.eks.amazonaws.com']]) {
                         sh "kubectl get svc -n microservices"
                }
            }
        }
    }
}

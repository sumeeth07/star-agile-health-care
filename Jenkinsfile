pipeline {
    agent any

    stages {
        stage('Checkout Code from GitHub') {
            steps {
                git url: 'https://github.com/sumeeth07/star-agile-health-care.git'
                echo 'GitHub URL checked out'
            }
        }
        stage('Compile Code') {
            steps {
                echo 'Starting compilation'
                sh 'mvn compile'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('QA Check') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sumith596/healthcareme:latest .'
                sh 'docker run -itd -p 8082:8082 sumith596/healthcareme:latest'
            }
        }
           stage('log into dockerhub and push the image to docker hub'){
            steps {
                withCredentials([string(credentialsId: 'dockerhubpass', variable: 'dockerhubpass')]) {
                sh 'docker login -u sumith596 -p ${dockerhubpass}'
                sh 'docker push sumith596/healthcareme:latest'
}
            }
        }
        stage('deploy using ansible'){
            steps{
                ansiblePlaybook become: true, credentialsId: 'ansible', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml', sudoUser: null, vaultTmpPath: ''
            }
        }
        stage('Kubernetes Deployment') {
    
    steps {
        script {
            echo 'Deploying application to Kubernetes'
            kubernetesDeploy(
                configs: 'deploymentservice.yaml',  // This is your Kubernetes manifest file
                kubeconfigId: 'k8sconfigpwd'        // Jenkins credentials ID for kubeconfig
            )
        }
    }
}

}
}

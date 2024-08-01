pipeline {
    agent any
    environment {
        GIT_CREDENTIALS_ID = 'github-cred'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-pwd'  
        KUBECONFIG_CREDENTIALS_ID = 'k8sconfigpwd'  
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/sumith07/star-agile-health-care.git', 
                    branch: 'master', 
                    credentialsId: "${GIT_CREDENTIALS_ID}"
            }
        }
        stage('Build Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    sh 'docker build -t sumith596/healthcareme:v1 .'
                }
            }
        }
        stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_CREDENTIALS_ID}", passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push sumith596/healthcareme:v1'
                }
            }
        }
        stage('Deploy to k8s') {
            when { branch 'master' }
            steps {
                script {
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: "${KUBECONFIG_CREDENTIALS_ID}")
                }
            }
        }
    }
}


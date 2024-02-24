pipeline {
    agent any

    tools {
        maven 'maven'
    }

    stages {
        stage('Build Maven') {
            steps {
                checkout scm
                script {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    sh 'docker build -t manthesh/devops-integration:latest .'
                }
            }
        }

        stage('Push image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'new', variable: 'dockerhub')]) {
                        sh "docker login -u manthesh -p ${dockerhub}"
                        sh 'docker push manthesh/devops-integration:latest'
                    }
                }
            }
        }

        stage('Deploy to k8s') {
            steps {
                script {
                    def kubeconfig = credentials('k8sconfigpwd')
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: kubeconfig)
                }
            }
        }
    }
}

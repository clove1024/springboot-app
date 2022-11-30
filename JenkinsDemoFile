pipeline {
    agent any
    tools {
        maven "maven3"
    }
    environment {
        registry = "855607364597.dkr.ecr.ap-northeast-2.amazonaws.com/demo-app"
    }

    stages {
        stage('CheckOut') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/clove1024/springboot-app']]])
            }
        }
        stage('BuildJar') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('BuildImage') {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        stage('ImagePushToECR') {
            steps {
                sh "aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin 855607364597.dkr.ecr.ap-northeast-2.amazonaws.com"
                sh "docker push $registry:latest"
            }
        }
        stage('DeployK8S') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'mrn-k8s-config', namespace: 'mrm', serverUrl: '') {
                    sh 'kubectl apply -f eks-deploy-k8s.yaml'
                }
            }
        }
    }
}

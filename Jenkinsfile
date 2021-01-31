pipeline{
    agent any
    environment {
        registry = "camilanorambuena/docker-image"
        registryCredential = 'dockerhub'
        application = ''
    }
    tools{
        maven "mvn"
        dockerTool "docker"
    }
    agent {
    label 'docker' 
    }
    stages{
        stage('Clonar de github'){
            steps{
                git url: 'https://github.com/camilanorambuena/PEP3.git', branch: 'main'
            }
        }
        stage('Construyendo con maven'){
            steps{
                sh "mvn clean package"
            }
        }

        stage ('Construyendo imagen (Docker)') {
            steps{
                script {
                    application = docker.build("camilanorambuena/docker-image:${env.BUILD_ID}")
                }
            }
        }

        stage ('imagen Push') {
            steps {
                
                script {
                    docker.withRegistry('', registryCredential) {
                            application.push("latest")
                            application.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy app en kubernetes'){
            steps{
                script {
                        kubernetesDeploy(configs: "hola-mundo.yaml", kubeconfigId: "mykubeconfig")
                    }
            }
        }
    }
}
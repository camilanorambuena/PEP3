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

        stage('Deploy app en kubernetes'){
            steps{
                script {
                        kubernetesDeploy(configs: "hola-mundo.yaml", kubeconfigId: "mykubeconfig")
                    }
            }
        }
    }
}
pipeline{
    
    environment {
        registry = "camilanorambuena/docker-image"
        registryCredential = 'dockerhub'
        application = ''
    }

    agent any

    stages{

        stage ('Clone and Build with Maven') {
            git url: 'https://github.com/camilanorambuena/PEP3.git', branch: 'main'
            withMaven {
            sh "mvn clean package"

            sh "mvn spotbugs:spotbugs"
            } 
        } 

        stage('Mega-Linter') {
            
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh "mega-linter-runner"
                }
            }
        }

        stage ('Build Image') {
            steps{
                script {
                    application = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }

        stage ('Push image') {
            steps {
                
                script {
                    docker.withRegistry('', registryCredential) {
                            application.push("latest")
                            application.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy'){
            steps{
                script {
                        kubernetesDeploy(configs: "hola-mundo.yaml", kubeconfigId: "mykubeconfig")
                    }
            }
        }
    }
}
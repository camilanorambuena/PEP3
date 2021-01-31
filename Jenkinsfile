pipeline{
    
    environment {
        registry = "camilanorambuena/docker-image"
        registryCredential = 'dockerhub'
        application = ''
    }

    agent any

    tools{
        maven "M1"
        dockerTool "Docker"
    }

    stages{

        stage('Clone'){
            steps{
                git url: 'https://github.com/camilanorambuena/PEP3.git', branch: 'main'
            }
        }
        stage('Build and Unit Tests'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('Stationary Analysis'){
            steps{
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
pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "camilanorambuena/docker-image"
    }
    stages {
         stage('Build') {	
             steps {	
                echo 'Running build automation'	
                sh 'chmod +x ./gradlew'	
                sh './gradlew build --no-daemon'	
            }	
        }
       
        stage('Build Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'main'
            }
            steps {
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'k8s_svc_deploy.yaml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}

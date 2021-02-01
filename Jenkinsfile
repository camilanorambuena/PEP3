pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "camilanorambuena/docker-image"
        kubeconfig = 'kubeconfig'
    }
    stages {
         stage('Build') {	
             steps {	
                echo 'Running build automation'	
                sh 'chmod +x ./gradlew'	
                sh './gradlew build --no-daemon'	
            }	
        }
       
        stage('Docker Imagen') {
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                }
            }
        }
        stage('Push para Docker Imagen') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy en kubernetes') {
            steps {
                script {
                    kubernetesDeploy(
                        kubeconfigId: kubeconfig,
                        configs: 'k8s_svc_deploy.yaml',
                    )
                }
            }
        }
    }
}

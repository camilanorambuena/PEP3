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
        stage('Deploy to PEP3') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' k8s_svc_deploy.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        stage('Deploy en kubernetes') {
            steps {
                kubernetesDeploy(
                    kubeconfigId: kubeconfig,
                    configs: 'k8s_svc_deploy.yaml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}

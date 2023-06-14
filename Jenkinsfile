pipeline {
    agent { label 'worker-node' }
    environment {
        registryCredentials = "nexus-registry"
        registry = "10.10.20.230:8083"
        imageName = "${env.registry}/sample-aspnet-app"
        latestImage = ""
        versionedImage = ""
    }
    stages {
        stage ('Code checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/funkycadet/sample-aspnet-app']]])
            }
        }
        // Building Docker images
        stage ('Building image') {
            steps {
                script {
                    // dockerImage = docker.build imageName
                    latestImage = docker.build("${env.imageName}:latest","./")
                    versionedImage = docker.build("${env.imageName}:1.0.0","./")
                }
            }
        }
        // Uploading Docker images into Nexus Registry
        stage('Uploading to Nexus') {
            steps {
                script {
                    docker.withRegistry( 'http://'+registry, registryCredentials ) {
                        latestImage.push()
                        versionedImage.push()
                    }
                }
            }
        }
    }
}

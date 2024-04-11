pipeline {
    agent any

    stages {
        stage("Code") {
            steps {
                git url: "https://github.com/mayaworld13/wanderlust_three_tier_deployment.git", branch: "main"
            }
        }
        stage("Installation") {
            steps {
                sh "chmod +x shellscript.sh"
                sh "./shellscript.sh"
            }
        }
        stage("Build & Test") {
            steps {
                dir("frontend") {
                    sh "docker build -t wanderlust:frontend ."
                }
                dir("backend") {
                    sh "docker build -t wanderlust:backend ."
                }
            }
        }
        stage("Push to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag wanderlust:frontend ${env.dockerHubUser}/wanderlust:frontend"
                    sh "docker tag wanderlust:backend ${env.dockerHubUser}/wanderlust:backend"
                    sh "docker push ${env.dockerHubUser}/wanderlust:frontend"
                    sh "docker push ${env.dockerHubUser}/wanderlust:backend"
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}

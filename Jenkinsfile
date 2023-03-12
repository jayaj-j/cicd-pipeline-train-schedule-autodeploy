pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "girijayas/train-schedule"
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Train_schedule_app'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_cred') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy_to_k8s') {
            steps {
				sshagent(['k8s']) {
                        sh 'scp -r -o StrictHostKeyChecking=no deploy_train_schedule.yml root@172.31.0.242:/tmp'
                        sh 'ssh root@172.31.0.242 kubectl apply -f /tmp/deploy_train_schedule.yml'
                       }
		}
        }
    }
}

pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'feat/dockerimage-to-harbor '
            }
            steps {
                script {
                    app = docker.build("NewtonCosta/train-schedule")
                    app.inside {
                        sh 'echo $(curl localhost:8080)'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'feat/dockerimage-to-harbor '
            }
            steps {
                script {
                    docker.withRegistry('demo.goharbor.io/jenkins-projects/REPOSITORY:latest', 'harbor_id') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
pipeline {
    agent any
    tools {
        go 'go1.18'
    }
    environment {
        CGO_ENABLED = 0 
        GOPATH = "${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}"
        GIN_MODE="release"
        DOCKERHUB_CREDENTIALS=credentials('4fe5936d-45b3-47b0-8601-c90fed5029ee')
        GIT_COMMIT = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }
    stages {
        stage('Test Source Code'){
            steps {
                sh 'go test'
            }
        }
        stage('Build Docker Image'){
            steps {
                sh 'docker build -t khhini/go-sample-app:${GIT_COMMIT} .'
            }
        }
        stage('Push Docker Image'){
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh "docker push khhini/go-sample-app:${GIT_COMMIT}"
                sh "docker image rm -f khhini/go-sample-app:${GIT_COMMIT}"
                sh "docker system prune -f"
            }
        }
        // stage('Deploy'){
        //     steps {
        //         ansiblePlaybook credentialsId: 'ansiblecd', disableHostKeyChecking: true, extras: '-e IMAGE_TAG=${GIT_COMMIT}', inventory: 'ansible-deployment/hosts', playbook: 'ansible-deployment/pb_deployment.yml'
        //     }
        // }
    }
}
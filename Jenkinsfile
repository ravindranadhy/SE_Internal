
properties([
    pipelineTriggers([
        githubPush()
    ])
])

node {

    def dockerImage = ""

    def dockerHubUser = "ravindrak2"
    def imageName = "oltrs"

    stage('Checkout') {
        echo 'Checking out source code from GitHub'

        git branch: 'main',
            url: 'https://github.com/ravindranadhy/SE_LAB2.git'
    }

    stage('Build') {
        echo 'Building OLTRS application using Maven'

        bat 'mvn clean compile'
    }

    stage('Test') {
        echo 'Running Maven test cases'

        bat 'mvn test'
    }

    stage('Package') {
        echo 'Packaging OLTRS application'

        bat 'mvn package -DskipTests'
    }

    stage('Docker Build') {
        echo 'Creating Docker image'

        dockerImage = "${dockerHubUser}/${imageName}:${env.BUILD_NUMBER}"

        bat "docker build -t ${dockerImage} ."
    }

    stage('Docker Hub Login and Push') {
        echo 'Pushing image to Docker Hub'

        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASSWORD'
            )
        ]) {
            bat '''
                @echo off
                echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USER% --password-stdin
                if errorlevel 1 exit /b 1
                docker push %DOCKER_USER%/oltrs:%BUILD_NUMBER%
                if errorlevel 1 exit /b 1
                docker logout
            '''
        }
    }

    stage('Complete') {
        echo 'OLTRS CI/CD Pipeline completed successfully'
        echo "Docker Image: ${dockerImage}"
    }
}

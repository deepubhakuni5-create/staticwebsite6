pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Ankit\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'

        DOCKERHUB_USERNAME = 'deepu09567'
        IMAGE_NAME = 'deepu09567/mywebsite'
        IMAGE_TAG = 'latest'
    }

    stages {

        stage('Docker Test') {
            steps {
                echo 'Testing Docker...'

                bat '''
                    "%DOCKER%" version
                '''
            }
        }

        stage('Docker Hub Login') {
            steps {
                echo 'Logging into Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-login',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                        "%DOCKER%" login -u "%DOCKER_USER%" -p "%DOCKER_PASS%"

                        if %ERRORLEVEL% NEQ 0 (
                            echo Docker Hub Login FAILED
                            exit /b 1
                        )

                        echo Docker Hub Login SUCCESSFUL
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                bat '''
                    "%DOCKER%" build -t %IMAGE_NAME%:%IMAGE_TAG% .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                bat '''
                    "%DOCKER%" push %IMAGE_NAME%:%IMAGE_TAG%

                    if %ERRORLEVEL% NEQ 0 (
                        echo Docker Push FAILED
                        exit /b 1
                    )

                    echo Docker Push SUCCESSFUL
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying website...'

                bat '''
                    "%DOCKER%" stop mywebsite 2>NUL
                    "%DOCKER%" rm mywebsite 2>NUL

                    "%DOCKER%" run -d ^
                        --name mywebsite ^
                        -p 8087:80 ^
                        %IMAGE_NAME%:%IMAGE_TAG%

                    if %ERRORLEVEL% NEQ 0 (
                        echo Deployment FAILED
                        exit /b 1
                    )

                    echo Deployment SUCCESSFUL
                '''
            }
        }

        stage('Verify') {
            steps {
                echo 'Checking running container...'

                bat '''
                    "%DOCKER%" ps
                '''
            }
        }
    }

    post {
        success {
            echo '================================'
            echo 'CI/CD PIPELINE SUCCESSFUL'
            echo '================================'
        }

        failure {
            echo '================================'
            echo 'CI/CD PIPELINE FAILED'
            echo '================================'
        }
    }
}

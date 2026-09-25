pipeline {
    agent any

    environment {
        DOCKER = 'C:\\Users\\Ankit\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'

        DOCKERHUB_USERNAME = 'deepu09567'
        IMAGE_NAME = 'deepu09567/mywebsite'
        IMAGE_TAG = 'latest'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'

                git branch: 'main',
                    url: 'https://github.com/deepubhakuni5-create/staticwebsite6.git'
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

        stage('Docker Login') {
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
                        echo %DOCKER_PASS% | "%DOCKER%" login -u "%DOCKER_USER%" --password-stdin

                        if %ERRORLEVEL% NEQ 0 (
                            echo Docker Hub login failed
                            exit /b 1
                        )

                        echo Docker Hub login successful
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                bat '''
                    "%DOCKER%" push %IMAGE_NAME%:%IMAGE_TAG%
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying container...'

                bat '''
                    "%DOCKER%" stop mywebsite 2>NUL
                    "%DOCKER%" rm mywebsite 2>NUL

                    "%DOCKER%" run -d ^
                        --name mywebsite ^
                        -p 8057:80 ^
                        %IMAGE_NAME%:%IMAGE_TAG%
                '''
            }
        }

        stage('Verify Deployment') {
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
            echo '========================================'
            echo 'CI/CD PIPELINE SUCCESSFUL'
            echo '========================================'
        }

        failure {
            echo '========================================'
            echo 'CI/CD PIPELINE FAILED'
            echo '========================================'
        }
    }
}

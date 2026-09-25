pipeline {
    agent any

    environment {
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
                    docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
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
                        docker login -u "%DOCKER_USER%" -p "%DOCKER_PASS%"

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
                    docker push %IMAGE_NAME%:%IMAGE_TAG%

                    if %ERRORLEVEL% NEQ 0 (
                        echo Docker image push failed
                        exit /b 1
                    )

                    echo Docker image pushed successfully
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying container...'

                bat '''
                    docker stop mywebsite 2>NUL
                    docker rm mywebsite 2>NUL

                    docker run -d ^
                        --name mywebsite ^
                        -p 8087:80 ^
                        %IMAGE_NAME%:%IMAGE_TAG%

                    if %ERRORLEVEL% NEQ 0 (
                        echo Container deployment failed
                        exit /b 1
                    )

                    echo Container deployed successfully
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Checking running container...'

                bat '''
                    docker ps
                '''
            }
        }
    }

    post {
        success {
            echo '======================================'
            echo 'CI/CD PIPELINE SUCCESSFUL'
            echo 'Docker image pushed successfully'
            echo 'Website running on port 8087'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo 'CI/CD PIPELINE FAILED'
            echo 'Check the stage where the error occurred'
            echo '======================================'
        }
    }
}

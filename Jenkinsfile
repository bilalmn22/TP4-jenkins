pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        timeout(time: 20, unit: 'MINUTES')
        retry(2)
    }

    environment {
        REPO_URL       = 'https://github.com/malkiAbdelhamid/book-app.git'
        BRANCH         = 'master'
        IMAGE_NAME     = 'bbook-application'
        CONTAINER_NAME = 'book-app-container'
        EXTERNAL_PORT  = '3000'
    }

    stages {

        stage('Cloning') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage('Installing') {
            steps {
                sh 'npm install'
            }
        }

        stage('Testing') {
            steps {
                sh 'npm test'
            }
            post {
                always {
                    echo 'Testing complete'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    echo "Removing old container..."
                    docker stop ${CONTAINER_NAME} || true
                    docker rm   ${CONTAINER_NAME} || true
                    echo "Launching a new container..."
                    docker run -d \\
                        --name ${CONTAINER_NAME} \\
                        -p ${EXTERNAL_PORT}:3000 \\
                        ${IMAGE_NAME}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    def running = sh(
                        script: "docker ps -q -f name=${CONTAINER_NAME}",
                        returnStdout: true
                    ).trim()

                    if (running == '') {
                        error("Deployment failed: container ${CONTAINER_NAME} is not running!")
                    } else {
                        echo "Container ${CONTAINER_NAME} is running successfully!"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Please check the logs.'
        }
    }
}
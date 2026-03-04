pipeline{
    agent{ docker {
        image 'node:14-alpine'
        args '-u root'
    }}
    stages{
        stage("clone"){
            steps{
            git "https://github.com/malkiAbdelhamid/book-app.git"
            }
        }
        stage("build"){
            steps{
            sh "npm install"
            }
        }
        stage("test"){
            steps{
            sh "npm test"
            }
            post{
            always{
                echo "Tests completed"
            }
            }
        }
       
    }
    post{
        success{
            echo "Pipeline completed successfully"
        }
        failure{
            echo "Pipeline failed"
        }
        always{
            echo "pipeline execution finished"
            cleanWs()
        }
    }
}
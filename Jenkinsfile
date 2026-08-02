pipeline {

    agent any

    stages {

        stage("Code Clone") {
            steps {
                git url: "https://github.com/JagdishDesaii/two-tier-flask-app.git", branch: "master"
            }
        }

        stage("Clean & Prepare") {
            steps {
                sh '''
                echo "Creating .dockerignore..."

                echo "mysql-data/" > .dockerignore
                echo ".git/" >> .dockerignore
                echo "__pycache__/" >> .dockerignore

                echo "Removing old containers..."

                docker ps -aq | xargs -r docker rm -f
                '''
            }
        }

        stage("Build") {
            steps {
                sh '''
                echo "Building Docker image..."

                docker build -t two-tier-flask-app .
                '''
            }
        }

        stage("Test") {
            steps {
                echo "Developer / Tester tests likh ke dega..."
            }
        }

        stage("Push to Docker Hub") {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "dockerHubCreds",
                        passwordVariable: "dockerHubPass",
                        usernameVariable: "dockerHubUser"
                    )
                ]) {

                    sh '''
                    echo "Logging into Docker Hub..."

                    docker login -u $dockerHubUser -p $dockerHubPass

                    docker tag two-tier-flask-app $dockerHubUser/two-tier-flask-app:latest

                    docker push $dockerHubUser/two-tier-flask-app:latest
                    '''
                }
            }
        }

        stage("Deploy") {
            steps {
                sh '''
                echo "Deploying application..."

                docker-compose down

                docker-compose up -d --build flask-app
                '''
            }
        }
    }

    post {
        success{
            script{
                emailext from: 'jdesai7178@gmail.com',
                to: 'jdesai7178@gmail.com',
                body: 'Build success for Demo CICD App',
                subject: 'Build success for Demo CICD App'
            }
        }
        failure{
            script{
                emailext from: 'jdesai7178@gmail.com',
                to: 'jdesai7178@gmail.com',
                body: 'Build Failed for Demo CICD App',
                subject: 'Build Failed for Demo CICD App'
            }
        }
        always {
            echo "Cleaning temporary files..."
            sh '''
            docker image prune -f
            '''
            
        }
    }
}

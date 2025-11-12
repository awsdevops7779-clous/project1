pipeline {
    agent any

    environment {
        NAME = "Riya"
        AGE  = "21"
    }

    stages {
        stage('Build') {
            steps {
                echo "Starting Build for $NAME, Age: $AGE"
                sh '''
                    echo "Compiling code..."
                    echo "Build successful!"
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running Tests..."
                retry(2) {
                    sh 'echo "Test step (retry if fails)"'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "Starting Deployment..."
                timeout(time: 10, unit: 'SECONDS') {
                    sh '''
                        echo "Deploying application..."
                        sleep 5
                        echo "Deployment completed"
                    '''
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished (success or failure)."
        }
        success {
            echo "✅ Pipeline executed successfully!"
        }
    }
}

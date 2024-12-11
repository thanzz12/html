pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from the Git repository
                    git scm  // This uses the Git configuration from the job configuration
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building the HTML files...'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Using SSH to deploy files to the server
                    sshagent(['${SSH_KEY}']) {
                        sh """
                            scp -r * username@${DEPLOY_SERVER}:${DEPLOY_PATH}
                        """
                    }
                }
            }
        }

        stage('Post Deployment') {
            steps {
                script {
                    echo 'Deployment completed!'
                }
            }
        }
    }

    post {
        success {
            echo 'The deployment was successful.'
        }
        failure {
            echo 'The deployment failed.'
        }
        always {
            echo 'This will always run, whether the build was successful or not.'
        }
    }
}


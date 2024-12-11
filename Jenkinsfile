pipeline {
    agent any

    environment {
        // Set your EC2 SSH credentials
        EC2_SSH_KEY = credentials('2750e6e6-4c38-4bd6-bb67-6c94587260b8')  // Jenkins credentials
        EC2_USER = 'ubuntu'  // EC2 instance username (commonly 'ec2-user' for Amazon Linux)
        EC2_IP = '13.200.251.8'  // EC2 public IP or DNS
        REMOTE_DIR = '/var/www/html'  // Directory on EC2 to deploy HTML files
        LOCAL_HTML_DIR = '/root/html'  // Local path to your HTML source code
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout your HTML code from your repository (e.g., GitHub)
                git branch: 'main', url: 'https://github.com/thanzz12/html.git'
            }
        }

        stage('Deploy to EC2') {
            steps {
                // Copy the HTML code to EC2 instance using SSH
                sshPublisher(publishers: [
                    sshPublisherDesc(
                        configName: 'AWS-EC2', 
                        transfers: [
                            sshTransfer(
                                sourceFiles: "${LOCAL_HTML_DIR}/**",  // Path to your local HTML files
                                remoteDirectory: "${REMOTE_DIR}",  // Remote path on EC2
                                removePrefix: "${LOCAL_HTML_DIR}",  // Remove prefix from files when copying
                                execCommand: 'sudo systemctl restart apache2'  // Command to restart web server (e.g., Apache)
                            )
                        ],
                        usePromotionTimestamp: false
                    )
                ])
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    // Example verification: check if the website is accessible (using curl)
                    def websiteUrl = "http://${EC2_IP}"
                    def response = sh(script: "curl -s -o /dev/null -w '%{http_code}' ${websiteUrl}", returnStdout: true).trim()
                    if (response == '200') {
                        echo "Deployment successful! Website is up and running."
                    } else {
                        error "Deployment failed! Website returned status code ${response}"
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed. Check the logs for details."
        }
    }
}


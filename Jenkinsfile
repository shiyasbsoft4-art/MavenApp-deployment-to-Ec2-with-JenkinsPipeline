pipeline {
    agent any

    tools {
        maven 'maven1'
        jdk 'JDK1'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shiyasbsoft4-art/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                        echo "Copying artifact to EC2..."
                        scp -o StrictHostKeyChecking=no \
                            target/demo-1.0.0.jar \
                            ubuntu@13.51.176.217:/opt/app/

                        echo "Starting application on EC2..."
                        ssh -o StrictHostKeyChecking=no ubuntu@13.51.176.217 << 'EOF'
                            pkill -f demo-1.0.0.jar || true
                            nohup java -jar /opt/app/demo-1.0.0.jar \
                                > /opt/app/app.log 2>&1 &
                        EOF

                        echo "Deployment command executed successfully"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }
        failure {
            echo "Deployment failed. Check Jenkins or EC2 logs."
        }
    }
}

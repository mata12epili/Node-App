pipeline{
    agent any
    environment{
        EC2_HOST = "13.204.75.230"
        SSH_CREDENTIALS_ID = "ec2-key"
        REMOTE_USER = "ubuntu"
        REMOTE_PATH = "/home/ubuntu/app"
        WEB_ROOT = "/var/www/html"
        SERVER = "nginx"
    }
    stages{
        stage('Build') {
            steps {
                echo 'Building...'
                sh "npm install"
                echo 'Build the app!'
                sh "npm run build"
                echo 'Build complete!'
                // Add your build commands here
            }
        }
        stage('Deploy') {
            steps {
                
                echo "starting deployment"
                sshagent(credentials: [env.SSH_CREDENTIALS_ID]) {
                    sh """
                     echo "creating remote directory"
                     ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST} 'mkdir -p ${REMOTE_PATH}'
                     echo "copying distribution to remote path"
                     scp -o StrictHostKeyChecking=no -r dist/* ${REMOTE_USER}@${EC2_HOST}:${REMOTE_PATH}/

                     echo "retarting nginx"
                     ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST} "
                     sudo rm -rf ${WEB_ROOT}/*
                     sudo cp -r ${REMOTE_PATH}/* ${WEB_ROOT}/
                     sudo systemctl restart ${SERVER}
                     "
                    """
                }
            }
        }
    }

}
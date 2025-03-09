pipeline {
    agent any

    environment {
        FTP_HOST = '148.72.90.37'
        FTP_USER = 'malladimurali'
        FTP_PASS = 'Srinu123$'
        FTP_TARGET_DIR = '/stage.uijavakit.com'  // Adjust according to GoDaddy hosting
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/chenchalasrinuui/deployments.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm install && npm run build'
            }
        }

        stage('Deploy to GoDaddy') {
            steps {
                script {
                    sh """
                    apt-get update && apt-get install -y lftp
                    lftp -c "open -u $FTP_USER,$FTP_PASS $FTP_HOST; mirror -R -e ./build $FTP_TARGET_DIR"
                    """
                }
            }
        }
    }
}

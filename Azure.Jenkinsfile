pipeline {
  agent any
  stages {
        stage('Install Dependencies') {
      steps {
        script {
          bat 'npm install'
                }
            }
        }

        stage('Build') {
      steps {
        script {
          bat 'npm run build'
                }
            }
        }

        stage('Deploy to Azure App Service') {
      steps {
          script {
               bat "cd C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\azure\\dist"
               bat "cd .."
               bat "mkdir MyReactApp"
               bat "xcopy dist\\* MyReactApp\\ /s /e"
               bat 'powershell -command "Compress-Archive -Path MyReactApp\\* -DestinationPath MyReactApp.zip'
               bat "rmdir /S /Q MyReactApp"
               bat "az login --service-principal -u $AZURE_SP_USER -p $AZURE_SP_PASSWORD --tenant $AZURE_TENANT_ID"
               bat "az staticwebapp deploy --resource-group web-deployment --name VTSReact --src-path C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\azure\\MyReactApp.zip"
               bat "del /F /Q MyReactApp.zip"
              }
            }
        }

    }
}

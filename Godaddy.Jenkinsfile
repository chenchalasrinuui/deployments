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
     stage('Upload to FTP') {
            steps {
                powershell '''
                # Define variables
                $ftpUser = "${env:FTP_USER}"
                $ftpPass = "${env:FTP_PASS}"
                $ftpHost = "${env:FTP_HOST}"
                $ftpTargetDir = "${env:FTP_TARGET_DIR}"
                $localPath = "${env:LOCAL_BUILD_PATH}"

                Write-Output "FTP User: $ftpUser"
                Write-Output "FTP Host: $ftpHost"
                Write-Output "Local Path: $localPath"

                # Ensure local path exists
                if (!(Test-Path -Path $localPath)) {
                    Write-Error "ERROR: Local build path not found: $localPath"
                    exit 1
                }

                $webclient = New-Object System.Net.WebClient
                $webclient.Credentials = New-Object System.Net.NetworkCredential($ftpUser, $ftpPass)

                Function Upload-Files {
                    param (
                        [string] $sourcePath,
                        [string] $targetPath
                    )

                    # Upload files
                    Get-ChildItem -Path $sourcePath -File | ForEach-Object {
                        $fileName = $_.Name
                        $filePath = $_.FullName
                        $remoteUri = "ftp://$ftpHost$targetPath/$fileName"

                        Write-Output "Uploading file: $filePath -> $remoteUri"
                        $webclient.UploadFile($remoteUri, $filePath)
                    }

                    # Upload directories recursively
                    Get-ChildItem -Path $sourcePath -Directory | ForEach-Object {
                        $folderName = $_.Name
                        $folderPath = $_.FullName
                        $subDir = "$targetPath/$folderName"
                        $remoteUri = "ftp://$ftpHost$subDir"

                        # Create folder on FTP server
                        $ftpRequest = [System.Net.WebRequest]::Create($remoteUri)
                        $ftpRequest.Credentials = New-Object System.Net.NetworkCredential($ftpUser, $ftpPass)
                        $ftpRequest.Method = [System.Net.WebRequestMethods+Ftp]::MakeDirectory
                        try {
                            $ftpRequest.GetResponse() | Out-Null
                            Write-Output "Created folder: $remoteUri"
                        } catch {
                            Write-Output "Folder may already exist: $remoteUri"
                        }

                        # Recursively upload files inside folder
                        Upload-Files -sourcePath $folderPath -targetPath $subDir
                    }
                }

                # Start uploading
                Upload-Files -sourcePath "$localPath" -targetPath "$ftpTargetDir"

                Write-Output "✅ FTP Upload Completed!"
                '''
            }
        }
}
}
    

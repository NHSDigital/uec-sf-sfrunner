pipeline {

    agent {
        label "jenkins-agent-local"
    }

    environment {
        // Define the Task version to install
        TASK_VERSION = '3.34.1'
        // Determine the architecture and set the download URL for Task
        TASK_DOWNLOAD_URL = ''
    }

    stages {
        stage('Prepare Environment') {
            steps {
                script {
                    // Detect architecture
                    if (isUnix()) {
                        def taskDownloadUrl = ''
                        def arch = sh(script: 'uname -m', returnStdout: true).trim()
                        echo "Detected architecture: ${arch}"

                        // Set the download URL based on architecture
                        if (arch == 'x86_64') {
                            taskDownloadUrl = "https://github.com/go-task/task/releases/download/v${env.TASK_VERSION}/task_linux_amd64.tar.gz"
                        } else if (arch.startsWith('arm') || arch.startsWith('aarch64')) {
                            taskDownloadUrl = "https://github.com/go-task/task/releases/download/v${env.TASK_VERSION}/task_linux_arm64.tar.gz"
                        } else {
                            error "Unsupported architecture: ${arch}"
                        }

                        // Download and install Task
                        sh """
                        curl -sL ${taskDownloadUrl} -o task.tar.gz
                        tar -xzf task.tar.gz
                        mv task /tmp
                        /tmp/task --version
                        """
                    } else {
                        error "Unsupported OS"
                    }
                }
            }
        }
        stage('Terraform plan and apply') {
            steps {
                sh "/tmp/task -t taskfile-infra.yml init"
                sh "/tmp/task -t taskfile-infra.yml plan"
                sh "/tmp/task -t taskfile-infra.yml apply"
            }
        }
    }

    post {
        always {
            sh "rm -f /tmp/task"
        }
    }
}
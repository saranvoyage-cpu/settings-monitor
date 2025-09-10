pipeline {
    agent any

    environment {
        SMTP_SERVER = 'smtp.gmail.com'
        SMTP_PORT = '587'
        SMTP_USERNAME = 'saranraj19don@gmail.com'
        SMTP_PASSWORD = credentials('SMTP_PASSWORD')
        TO_EMAIL = 'saranraj19don@gmail.com'
        MSPMTP_PATH = '/usr/bin/msmtp'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Detect Change in setting2.txt') {
            steps {
                script {
                    def changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD", returnStdout: true).trim()
                    if (changedFiles.contains("setting2.txt")) {
                        echo "setting2.txt was changed."

                        sh '''
                        export PATH=$PATH:/usr/bin
                        echo "defaults
auth on
tls on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
logfile ~/.msmtp.log

account default
host ${SMTP_SERVER}
port ${SMTP_PORT}
from ${SMTP_USERNAME}
user ${SMTP_USERNAME}
password ${SMTP_PASSWORD}" > ~/.msmtprc
                        chmod 600 ~/.msmtprc

                        echo -e "Subject: Sensitive File Changed Alert\n\nThe file 'setting2.txt' was modified in the repository.\nRepository: ${JOB_NAME}\nCommit: ${GIT_COMMIT}\nChanged by: ${BUILD_USER}" | ${MSPMTP_PATH} ${TO_EMAIL}
                        '''
                    } else {
                        echo "No change in setting2.txt."
                    }
                }
            }
        }
    }
}


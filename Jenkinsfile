pipeline {
    agent any

    environment {
        AZURE_HOST = '20.124.67.32'                  // Example: 20.124.67.32
        AZURE_USER = 'srivallabha'              // Example: srivallabha
        SSH_KEY_ID = 'azure-ssh-key'               // ID of your SSH key in Jenkins credentials
        APP_JAR = 'target/spring-petclinic-3.5.0-SNAPSHOT.jar'
        DEST_PATH = '/home/azure-user/app.jar'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/srivallabha01/petclinic-dev.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh './mvnw clean install'
            }
        }

        stage('Check JAR Output') {
            steps {
                sh 'ls -lh target/'
            }
        }

        stage('Copy JAR to Azure VM') {
            steps {
                sshagent([SSH_KEY_ID]) {
                    sh "scp -o StrictHostKeyChecking=no ${APP_JAR} ${AZURE_USER}@${AZURE_HOST}:${DEST_PATH}"
                }
            }
        }

        stage('Run Ansible Playbook on Azure VM') {
            steps {
                sshagent([SSH_KEY_ID]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ${AZURE_USER}@${AZURE_HOST} '
                      ansible-playbook -i /home/${AZURE_USER}/hosts /home/${AZURE_USER}/ansible/playbook.yml
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}

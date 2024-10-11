pipeline {
    agent any

    environment {
        // JFrog Artifactory credentials stored in Jenkins
        ARTIFACTORY_URL = 'http://184.73.68.163:8081/artifactory/'
        ARTIFACTORY_CREDENTIALS = credentials( 'jfrog-cred')
        ARTIFACTORY_REPO = 'geolocation'
        ZIP_FILENAME = 'ansible-playbook.zip'
        REMOTE_PATH = '/home/ec2-user/ansible-dev'
        ANSIBLE_SERVER = '52.7.22.161'
        SSH_CREDENTIALS = 'master_ansible-cred'
        BRANCH_NAME = 'main'
        PROJECT_URL = 'https://github.com/boateng0303/Ansible-w17.git'
    }

     stages{
        stage('Git Checkout'){
            steps{
                git branch: "${BRANCH_NAME}", \
                url: "${PROJECT_URL}"
          
            }
        }


        stage('Package Ansible Playbook') {
            steps {
                sh 'zip -r ${ZIP_FILENAME} *'
            }
        }

        stage('Upload to JFrog') {
            steps {
                script {
                    def server = Artifactory.server 'artifactory-instance'
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "${ZIP_FILENAME}",
                                "target": "${ARTIFACTORY_REPO}/"
                            }
                        ]
                    }"""
                    server.upload(uploadSpec)
                }
            }
        }

        stage('Deploy to Ansible Server') {
            steps {
                sshagent(credentials: [SSH_CREDENTIALS]) {
                    sh "scp ${ZIP_FILENAME} ec2-user@${ANSIBLE_SERVER}:${REMOTE_PATH}/"
                }
            }
        }
    }

   

pipeline {
    agent any

    environment {
        // JFrog Artifactory credentials stored in Jenkins
        ARTIFACTORY_URL = 'http://184.73.68.163:8081/artifactory/'
        JROG_CRED = 'jfrog-cred'
        REPO = 'geolocation'
        ZIP_FILENAME = 'ansible-playbook.zip'
        REMOTE_PATH = '/home/ec2-user/ansible-dev'
        ANSIBLE_SERVER = '52.7.22.161'
        SSH_CREDENTIALS = 'master_ansible-cred'
        BRANCH_NAME = 'main'
        PROJECT_URL = 'https://github.com/boateng0303/Ansible-w17.git'
        ARTIFACTPATH = 'target/*.zip'
        ARTIFACTTARGETPATH = 'ansible-playbook_${BUILD_ID}'
        
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: "${BRANCH_NAME}", url: "${PROJECT_URL}"
            }
        }

        stage('Package Ansible Playbook') {
            steps {
                sh 'zip -r ${ZIP_FILENAME} *'
            }
        }

            stage('Upload Jar to Jfrog'){
            steps{
                withCredentials([usernamePassword(credentialsId: "${JFROG_CRED}", \
                 usernameVariable: 'ARTIFACTORY_USER', passwordVariable: 'ARTIFACTORY_PASSWORD')]) {
                    script {
                        // Define the artifact path and target location
                        //def artifactPath = 'target/*.jar'
                        //def targetPath = "release_${BUILD_ID}.jar"

                        // Upload the artifact using curl
                        sh """
                            curl -u ${ARTIFACTORY_USER}:${ARTIFACTORY_PASSWORD} \
                                 -T ${ARTIFACTPATH} \
                                 ${ARTIFACTORY_URL}/${REPO}/${ARTIFACTTARGETPATH}
                        """
            }
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

    post {
        always {
            cleanWs() // Clean up the workspace
        }
    }
}

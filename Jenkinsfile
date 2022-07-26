pipeline {
    agent any
    environment{
        BRANCH_VAR  = "MAIN"
        GIT_PATH    = "/var/jenkins_home/workspace/web-page-multibranch_main/webpage_count"
    }
    stages {
        stage('Check') {
            steps {
                script {
                    if (fileExists("${env.GIT_PATH}/index.html")) {
                        sh "rm -rf ${env.GIT_PATH}"
                        echo "Folder properly deleted, the repository can be cloned"
                    }else {
                        echo "The folder doesn't exist, the repository can be cloned."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh """
                    echo "Runnign from Build stage"
                    git clone --branch main --single-branch https://github.com/andymartinezot/webpage_count.git
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                sshagent(credentials : ['remote-credentials']){
                    sh """
                    ssh -o StrictHostKeyChecking=no remote_user@nginx_web whoami
                    scp -o StrictHostKeyChecking=no ${env.GIT_PATH}/* remote_user@nginx_web:/var/www/html/
                    """
                }
            }
        }
        stage("Final Test") {
            steps {
                script {
                    sh "curl -o - -I -s http://192.168.0.28:8081 | head -n 1"
                    echo "if the response is a 200 OK the pipeline was executed correctly"
                }
            }
        }
    }
}

pipeline {
  agent any
  stages {

    stage('Pull project') {
      steps {
        script {
          git branch: '<branch_name>', credentialsId: '<jenkins_credentials>', url: 'repo_ssh_url'
        }
      }
    }

    stage('checkout feature-general-plugin') {
      steps {
        sh 'git checkout <branch_name>'
        sh 'git branch'
        sh 'git config user.email "<your_email>"'
        sh 'git config user.name "<name>"'
      }
    }

    stage('Copy Artifact from remote/upstream Job') {
      steps {
          script {
                 step ([$class: 'CopyArtifact',
                 projectName: "<up_stream_project_name>/master",
                 excludes: 'gradle-wrapper.jar',
                 filter: "**/*.jar",
                 flatten: true,
                 target: 'files/plugins/<path>']);
          }
      }
    }

    stage('Delete build and Gradle Folders') {
        steps {
            script{
                dir('files/plugins/<path>'){
                    sh 'rm -rf build/'
                    sh 'rm -rf gradle/'
                    sh 'rm -rf gradle-wrapper.jar'
                }
            }
        }
    }

    stage('Rename Artifact') {
        steps {
            script{
                dir('files/plugins/<path>'){
                    sh 'echo Will rename file here'
                }
            }
        }
    }

    stage('Commit Artifacts') {
        steps {
              sh 'git add .'
              sh "git commit -m 'New feature'"
        }
    }

    stage('Push new jar') {
      steps {
        withCredentials([sshUserPrivateKey(credentialsId: '<jenkins_credentials>', keyFileVariable: 'SSH_KEY')]) {
          sh 'echo ssh -i $SSH_KEY -l git -o StrictHostKeyChecking=no \\"\\$@\\" >> local_ssh.sh'
          sh 'chmod +x local_ssh.sh'
          sh 'chmod 777 ./local_ssh.sh'
          withEnv(['GIT_SSH=./local_ssh.sh']) {
            sh 'git push origin HEAD:<branch_name>'
          }
        }
      }
    }
  }
}

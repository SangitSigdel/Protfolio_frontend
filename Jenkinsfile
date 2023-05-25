void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/SangitSigdel/Protfolio_frontend"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

def sendEmail(String status) {
    BUILD_TRIGGER_BY=$(curl -k --silent ${BUILD_URL}/api/xml | tr '<' '\n' | egrep '^userId>|^userName>' | sed 's/.*>//g' | sed -e '1s/$/ \//g' | tr '\n' ' ')
    emailext body: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' has finished.",
             recipientProviders: [[$class: 'CulpritsRecipientProvider']],
             subject: "${status}: Job '${env.JOB_NAME}'",
             to: BUILD_TRIGGER_BY
}




pipeline {
    agent any
    options {
      timeout(time: 12, unit: 'MINUTES') 
    } 
    stages {
        
        stage('Build') { 
            steps {
                setBuildStatus("", "PENDING");
                echo "1st Building..... "
                sh "npm install"
            }
        }
        stage('Test') { 
            steps {
                echo "2nd Testing.........."
                sh "npm run test"
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Run the shell command to get the current Git branch and capture the output
                    def branchName = sh(returnStdout: true, script: 'git ls-remote --heads origin | grep $(git rev-parse HEAD) | cut -d / -f 3').trim()

                    // Print the branch name
                    if(branchName=="develop"){
                        sh 'npm run build'
                        sh 'scp -r -i /var/jenkins_home/web_server.pem build/* ubuntu@18.133.117.97:/var/www/Protfolio_web_app/'
                    }
                    else {
                        echo "============DEPLOYMENT WILL BE PERFORMED AFTER MERGED TO DEVELOP BRANCH==================="
                    }
                }
            }
        }
    }

        post {
            success {
                setBuildStatus("Build succeeded ✅", "SUCCESS"); 
                script {
                    sendEmail("success")
                }
            }
            failure {
                script {
                    sendEmail("Build Failed")
                }
                setBuildStatus("Build failed ❌ ", "FAILURE");
            }

        }
    }
   
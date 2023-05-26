void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/SangitSigdel/Protfolio_frontend"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}


pipeline {
    agent any
    options {
      timeout(time: 12, unit: 'MINUTES') 
      buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '10', daysToKeepStr: '', numToKeepStr: '10')
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
                        sh 'scp -r -i /var/jenkins_home/web_server.pem build/* ubuntu@35.178.20.24:/var/www/Protfolio_web_app/'
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
                echo "The jenkins user email is: ${env.JENKINS_USER_EMAIL}"
                emailext subject: "Jenkins Pipeline Failure",
                body: """ Jenkins Pipeline Passed. Check the logs for more details.""",
                to: "sangit.sigdel@gmail.com" 
                 
            }
            failure {

                echo "The jenkins user email is: ${env.JENKINS_USER_EMAIL}"
                setBuildStatus("Build failed ❌ ", "FAILURE");
                emailext subject: "Jenkins Pipeline Failure",
                body: """ Jenkins Pipeline failed. Check the logs for more details.""",
                to: "sangit.sigdel@gmail.com" 
                
            }
            always {
                cleanWs(cleanWhenNotBuilt: false,
                        deleteDirs: true,
                        disableDeferredWipeout: true,
                        notFailBuild: true,
                        patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                                    [pattern: '.propsfile', type: 'EXCLUDE']])
            }

        }
    }
   
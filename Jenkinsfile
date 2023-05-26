import hudson.model.User


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
                        sh 'scp -r -i /var/jenkins_home/web_server.pem build/* ubuntu@13.40.134.247:/var/www/Protfolio_web_app/'
                    }
                    else {
                        echo "============DEPLOYMENT WILL BE PERFORMED AFTER MERGED TO DEVELOP BRANCH==================="
                    }
                }
            }
        }
         stage('Get User Email') {
            steps {
                script {
                    def buildCauses = currentBuild.getBuildCauses()
                    def userEmail

                    for (cause in buildCauses) {
                        if (cause instanceof hudson.model.Cause$UserIdCause) {
                            userEmail = cause.getUser().getProperty(hudson.tasks.Mailer$UserProperty).getAddress()
                            break
                        }
                    }

                    echo "User Email: ${userEmail}"
                }
            }
        }
    }

        post {
            success {
                setBuildStatus("Build succeeded ✅", "SUCCESS"); 
                 
            }
            failure {
                setBuildStatus("Build failed ❌ ", "FAILURE");
                mail bcc: '', body: "<b>Example</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR CI: Project name -> ${env.JOB_NAME}", to: "sangit.sigdel@gmail.com";  
            }
            always {
                cleanWs(cleanWhenNotBuilt: false,
                        deleteDirs: true,
                        disableDeferredWipeout: true,
                        notFailBuild: true,
                        patterns: [[pattern: '.gitignore', type: 'INCLUDE'],
                                    [pattern: '.propsfile', type: 'EXCLUDE']])
                println "Logged-in user email: ${userEmail}"
            }

        }
    }
   
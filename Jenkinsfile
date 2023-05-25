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
    } 
    stages {
        
        stage('Build') { 
            steps {
                setBuildStatus("", "PENDING");
                // cleanWs();
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
                        sh 'scp -r -i /var/jenkins_home/web_server.pem build/* ubuntu@18.169.241.165:/var/www/Protfolio_web_app/'
                    }
                    else {
                        echo "============DEPLOYMENT WILL BE PERFORMED AFTER MERGED TO DEVELOP BRANCH===================="
                    }
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
   
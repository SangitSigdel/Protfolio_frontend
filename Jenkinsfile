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
                echo "1st Building....... "
                sh "npm install"
            }
        }
        stage('Test') { 
            steps {
                echo "2nd Testing........."
                sh "npm run test"
            }
        }
        stage('Deploy') 
            { 
                steps {
                    script {
                        def output = sh(returnStdout: true, script: 'git branch --show-current')
                        echo "-----------------------------"
                        echo "${output}"   
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

        }
    }
   
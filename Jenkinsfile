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
        stage('Deploy') {
            steps {
                script {
                    // Get the current branch name
                    def gitBranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()

                    // Check if the branch is "develop"
                    if (gitBranch == 'develop') {
                        // Deployment steps for the "develop" branch
                        echo "Deploying branch 'develop'"
                        // Add your deployment commands or steps here
                    } else {
                        // Skip deployment for other branches
                        echo "Skipping deployment for branch '${gitBranch}'"
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

        }
    }
   
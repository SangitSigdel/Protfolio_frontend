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
                        current_branch= sh(
                            script: 'git branch --show-current',
                            returnStdout: true
                        ).trim()
                        echo "-----------------------------"
                        echo "${current_branch}"
                    if ( current_branch === 'develop') {
                        steps{
                            sh 'npm run build'
                            sh 'scp -r -i /var/jenkins_home/web_server.pem build/* ubuntu@18.170.48.210:/var/www/Protfolio_web_app/'
                        }
                    } else {
                        echo '==== deoploy will continue after merging to develop branch ====='
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
   
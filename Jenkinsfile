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

        stage ('Start') {
            steps{ 
                echo "Starting......."
                // git branch: "", url: 'git@github.com:SangitSigdel/Protfolio_frontend.git'    
            }
            
        }
        stage('Build') { 
            steps {
                echo "1st Building..... "
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
                 steps{
                    sh 'npm run build'
                    sh 'scp -r -i /var/jenkins_home/workspace/web_server.pem build/* ubuntu@18.134.7.226:/var/www/Protfolio_web_app/'
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
   
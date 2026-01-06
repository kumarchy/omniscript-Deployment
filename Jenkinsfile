pipeline {
  agent any

   tools {
    nodejs 'NodeJS-LTS'  // This should match the name you configured
  }

  environment {
    SF_INSTANCE_URL = "https://test.salesforce.com"
    SF_AUTOUPDATE_DISABLE = "true"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Authenticate Salesforce') {
      steps {
        withCredentials([
          string(credentialsId: 'SF_OMCLIENT_ID', variable: 'CLIENT_ID'),
          string(credentialsId: 'SF_OMUSERNAME', variable: 'USERNAME'),
          file(credentialsId: 'SF_OMJWT_KEY', variable: 'JWT_KEY')
        ]) {
          bat """
          sf org login jwt ^
            --client-id %CLIENT_ID% ^
            --jwt-key-file "%JWT_KEY%" ^
            --username %USERNAME% ^
            --instance-url %SF_INSTANCE_URL% ^
            --alias TargetOrg
          """
        }
      }
    }

    // stage('Bootstrap OmniStudio') {
    //   steps {
    //     bat """
    //     npx vlocity --sfdx.username TargetOrg -job bootstrap.yaml packDeploy
    //     """
    //   }
    // }

    stage('Deploy OmniScripts & DataRaptors') {
      steps {
        bat """
        npx vlocity --sfdx.username TargetOrg -job platform.yaml packDeploy --verbose
        """
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/VlocityBuild.log', allowEmptyArchive: true
    }
    failure {
      echo '❌ OmniStudio deployment failed'
    }
    success {
      echo '✅ OmniStudio deployment successful'
    }
  }
}

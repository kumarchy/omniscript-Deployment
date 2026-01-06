// pipeline {
//   agent any

//    tools {
//     nodejs 'NodeJS-LTS'  // This should match the name you configured
//   }

//   environment {
//     SF_INSTANCE_URL = "https://test.salesforce.com"
//     SF_AUTOUPDATE_DISABLE = "true"
//   }

//   stages {

//     stage('Checkout') {
//       steps {
//         checkout scm
//       }
//     }

//     stage('Authenticate Salesforce') {
//       steps {
//         withCredentials([
//           string(credentialsId: 'SF_OMCLIENT_ID', variable: 'CLIENT_ID'),
//           string(credentialsId: 'SF_OMUSERNAME', variable: 'USERNAME'),
//           file(credentialsId: 'SF_OMJWT_KEY', variable: 'JWT_KEY')
//         ]) {
//           bat """
//           sf org login jwt ^
//             --client-id %CLIENT_ID% ^
//             --jwt-key-file "%JWT_KEY%" ^
//             --username %USERNAME% ^
//             --instance-url %SF_INSTANCE_URL% ^
//             --alias TargetOrg
//           """
//         }
//       }
//     }

//     stage('Deploy OmniScripts & DataRaptors') {
//       steps {
//         bat """
//         npx vlocity --sfdx.username TargetOrg -job platform.yaml packDeploy --verbose
//         """
//       }
//     }
//   }

//   post {
//     always {
//       archiveArtifacts artifacts: '**/VlocityBuild.log', allowEmptyArchive: true
//     }
//     failure {
//       echo '❌ OmniStudio deployment failed'
//     }
//     success {
//       echo '✅ OmniStudio deployment successful'
//     }
//   }
// }


pipeline {
  agent any

  tools {
    nodejs 'NodeJS-LTS'
  }

  environment {
    SF_INSTANCE_URL = "https://test.salesforce.com"
    SF_AUTOUPDATE_DISABLE = "true"
    RECIPIENT_EMAIL = "lamphukumar228@gmail.com"  // Change this
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

    stage('Deploy OmniScripts & DataRaptors') {
      steps {
        script {
          env.DEPLOY_START_TIME = new Date().format('yyyy-MM-dd HH:mm:ss')
        }
        bat """
        npx vlocity --sfdx.username TargetOrg -job platform.yaml packDeploy --verbose
        """
        script {
          env.DEPLOY_END_TIME = new Date().format('yyyy-MM-dd HH:mm:ss')
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '**/vlocity-temp/logs/*.yaml', allowEmptyArchive: true
    }
    
    success {
      emailext (
        subject: "✅ SUCCESS: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
        body: """
          <html>
          <body style="font-family: Arial, sans-serif;">
            <div style="background-color: #4CAF50; padding: 20px; color: white;">
              <h2>✅ Deployment Successful!</h2>
            </div>
            <div style="padding: 20px;">
              <h3>Build Information</h3>
              <table style="border-collapse: collapse; width: 100%;">
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Build Number:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">#${env.BUILD_NUMBER}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Job Name:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${env.JOB_NAME}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Started By:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${env.BUILD_USER ?: 'Jenkins'}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Start Time:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${env.DEPLOY_START_TIME ?: 'N/A'}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>End Time:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${env.DEPLOY_END_TIME ?: 'N/A'}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Duration:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${currentBuild.durationString.replace(' and counting', '')}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Target Org:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">TargetOrg (${env.SF_INSTANCE_URL})</td>
                </tr>
              </table>
              
              <h3 style="margin-top: 20px;">Deployed Components</h3>
              <ul>
                <li>✓ 17 DataRaptors</li>
                <li>✓ 2 OmniScripts</li>
              </ul>
              
              <p style="margin-top: 20px;">
                <a href="${env.BUILD_URL}" style="background-color: #4CAF50; color: white; padding: 10px 20px; text-decoration: none; border-radius: 5px;">View Build Details</a>
              </p>
            </div>
          </body>
          </html>
        """,
        to: "${env.RECIPIENT_EMAIL}",
        mimeType: 'text/html',
        attachLog: true
      )
      echo '✅ OmniStudio deployment successful - Email sent!'
    }
    
    failure {
      emailext (
        subject: "❌ FAILED: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
        body: """
          <html>
          <body style="font-family: Arial, sans-serif;">
            <div style="background-color: #f44336; padding: 20px; color: white;">
              <h2>❌ Deployment Failed!</h2>
            </div>
            <div style="padding: 20px;">
              <h3>Build Information</h3>
              <table style="border-collapse: collapse; width: 100%;">
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Build Number:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">#${env.BUILD_NUMBER}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Job Name:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${env.JOB_NAME}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Failed At:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">${new Date().format('yyyy-MM-dd HH:mm:ss')}</td>
                </tr>
                <tr>
                  <td style="padding: 8px; border: 1px solid #ddd;"><strong>Target Org:</strong></td>
                  <td style="padding: 8px; border: 1px solid #ddd;">TargetOrg (${env.SF_INSTANCE_URL})</td>
                </tr>
              </table>
              
              <h3 style="margin-top: 20px;">Error Details</h3>
              <p style="background-color: #ffebee; padding: 10px; border-left: 4px solid #f44336;">
                Please check the build logs for detailed error information.
              </p>
              
              <p style="margin-top: 20px;">
                <a href="${env.BUILD_URL}console" style="background-color: #f44336; color: white; padding: 10px 20px; text-decoration: none; border-radius: 5px;">View Console Logs</a>
              </p>
            </div>
          </body>
          </html>
        """,
        to: "${env.RECIPIENT_EMAIL}",
        mimeType: 'text/html',
        attachLog: true
      )
      echo '❌ OmniStudio deployment failed - Email sent!'
    }
  }
}
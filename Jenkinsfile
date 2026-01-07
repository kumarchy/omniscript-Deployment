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

// ----------------------------------

// pipeline {
//   agent any

//   tools {
//     nodejs 'NodeJS-LTS'
//   }

//   environment {
//     SF_INSTANCE_URL = "https://test.salesforce.com"
//     SF_AUTOUPDATE_DISABLE = "true"
//     RECIPIENT_EMAIL = "lamphukumar228@gmail.com"  // Change this
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
//         script {
//           env.DEPLOY_START_TIME = new Date().format('yyyy-MM-dd HH:mm:ss')
//         }
//         bat """
//         npx vlocity --sfdx.username TargetOrg -job platform.yaml packDeploy --verbose
//         """
//         script {
//           env.DEPLOY_END_TIME = new Date().format('yyyy-MM-dd HH:mm:ss')
//         }
//       }
//     }
//   }

//   post {
//     always {
//       archiveArtifacts artifacts: '**/vlocity-temp/logs/*.yaml', allowEmptyArchive: true
//     }
    
//     success {
//       emailext (
//         subject: "✅ SUCCESS: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
//         body: """
//           <html>
//           <body style="font-family: Arial, sans-serif;">
//             <div style="background-color: #4CAF50; padding: 20px; color: white;">
//               <h2>✅ Deployment Successful!</h2>
//             </div>
//             <div style="padding: 20px;">
//               <h3>Build Information</h3>
//               <table style="border-collapse: collapse; width: 100%;">
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Build Number:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">#${env.BUILD_NUMBER}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Job Name:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${env.JOB_NAME}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Started By:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${env.BUILD_USER ?: 'Jenkins'}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Start Time:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${env.DEPLOY_START_TIME ?: 'N/A'}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>End Time:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${env.DEPLOY_END_TIME ?: 'N/A'}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Duration:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${currentBuild.durationString.replace(' and counting', '')}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Target Org:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">TargetOrg (${env.SF_INSTANCE_URL})</td>
//                 </tr>
//               </table>
              
//               <h3 style="margin-top: 20px;">Deployed Components</h3>
//               <ul>
//                 <li>✓ 17 DataRaptors</li>
//                 <li>✓ 2 OmniScripts</li>
//               </ul>
              
//               <p style="margin-top: 20px;">
//                 <a href="${env.BUILD_URL}" style="background-color: #4CAF50; color: white; padding: 10px 20px; text-decoration: none; border-radius: 5px;">View Build Details</a>
//               </p>
//             </div>
//           </body>
//           </html>
//         """,
//         to: "${env.RECIPIENT_EMAIL}",
//         mimeType: 'text/html',
//         attachLog: true
//       )
//       echo '✅ OmniStudio deployment successful - Email sent!'
//     }
    
//     failure {
//       emailext (
//         subject: "❌ FAILED: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
//         body: """
//           <html>
//           <body style="font-family: Arial, sans-serif;">
//             <div style="background-color: #f44336; padding: 20px; color: white;">
//               <h2>❌ Deployment Failed!</h2>
//             </div>
//             <div style="padding: 20px;">
//               <h3>Build Information</h3>
//               <table style="border-collapse: collapse; width: 100%;">
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Build Number:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">#${env.BUILD_NUMBER}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Job Name:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${env.JOB_NAME}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Failed At:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">${new Date().format('yyyy-MM-dd HH:mm:ss')}</td>
//                 </tr>
//                 <tr>
//                   <td style="padding: 8px; border: 1px solid #ddd;"><strong>Target Org:</strong></td>
//                   <td style="padding: 8px; border: 1px solid #ddd;">TargetOrg (${env.SF_INSTANCE_URL})</td>
//                 </tr>
//               </table>
              
//               <h3 style="margin-top: 20px;">Error Details</h3>
//               <p style="background-color: #ffebee; padding: 10px; border-left: 4px solid #f44336;">
//                 Please check the build logs for detailed error information.
//               </p>
              
//               <p style="margin-top: 20px;">
//                 <a href="${env.BUILD_URL}console" style="background-color: #f44336; color: white; padding: 10px 20px; text-decoration: none; border-radius: 5px;">View Console Logs</a>
//               </p>
//             </div>
//           </body>
//           </html>
//         """,
//         to: "${env.RECIPIENT_EMAIL}",
//         mimeType: 'text/html',
//         attachLog: true
//       )
//       echo '❌ OmniStudio deployment failed - Email sent!'
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
    
    // Configure recipients directly here (easier approach)
    EMAIL_RECIPIENTS = "lamphukumar228@gmail.com,chaudharykumar228@gmail.com"
    CC_LIST = "22ad024@kpriet.ac.in"
    SENDER_NAME = "OmniStudio Deployment Bot"
    // REPLY_TO = "lamphukumar228@gmail.com"
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
      script {
        echo "📧 Sending success notification to: ${env.EMAIL_RECIPIENTS}"
        
        emailext (
          subject: "✅ SUCCESS: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
          body: """
            <html>
            <head>
              <style>
                body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
                .header { background-color: #4CAF50; padding: 20px; color: white; }
                .content { padding: 20px; background-color: #f5f5f5; }
                .info-box { background-color: white; padding: 20px; border-radius: 5px; margin-bottom: 20px; }
                table { border-collapse: collapse; width: 100%; }
                td { padding: 10px; border: 1px solid #ddd; }
                .label { background-color: #f9f9f9; font-weight: bold; }
                .button { background-color: #4CAF50; color: white; padding: 12px 25px; 
                         text-decoration: none; border-radius: 5px; display: inline-block; 
                         margin: 5px; font-size: 14px; }
                .footer { text-align: center; margin-top: 20px; color: #888; font-size: 12px; }
                h2, h3 { margin-top: 0; }
                ul { font-size: 16px; line-height: 1.8; }
              </style>
            </head>
            <body>
              <div class="header">
                <h2>✅ Deployment Successful!</h2>
              </div>
              <div class="content">
                <div class="info-box">
                  <h3 style="color: #333; border-bottom: 2px solid #4CAF50; padding-bottom: 10px;">
                    Build Information
                  </h3>
                  <table>
                    <tr>
                      <td class="label">Build Number:</td>
                      <td>#${env.BUILD_NUMBER}</td>
                    </tr>
                    <tr>
                      <td class="label">Job Name:</td>
                      <td>${env.JOB_NAME}</td>
                    </tr>
                    <tr>
                      <td class="label">Started By:</td>
                      <td>${env.BUILD_USER ?: 'Automated Trigger'}</td>
                    </tr>
                    <tr>
                      <td class="label">Start Time:</td>
                      <td>${env.DEPLOY_START_TIME ?: 'N/A'}</td>
                    </tr>
                    <tr>
                      <td class="label">End Time:</td>
                      <td>${env.DEPLOY_END_TIME ?: 'N/A'}</td>
                    </tr>
                    <tr>
                      <td class="label">Duration:</td>
                      <td>${currentBuild.durationString.replace(' and counting', '')}</td>
                    </tr>
                    <tr>
                      <td class="label">Target Org:</td>
                      <td>TargetOrg (${env.SF_INSTANCE_URL})</td>
                    </tr>
                  </table>
                </div>
                
                <div class="info-box">
                  <h3 style="color: #333; border-bottom: 2px solid #4CAF50; padding-bottom: 10px;">
                    Deployed Components
                  </h3>
                  <ul>
                    <li>✓ <strong>17 DataRaptors</strong> - Successfully deployed and activated</li>
                    <li>✓ <strong>2 OmniScripts</strong> - Successfully deployed and activated</li>
                  </ul>
                </div>
                
                <div style="text-align: center; margin-top: 30px;">
                  <a href="${env.BUILD_URL}" class="button">View Build Details</a>
                  <a href="${env.BUILD_URL}console" class="button" style="background-color: #2196F3;">View Console Logs</a>
                </div>
                
                <div class="footer">
                  <p>This is an automated notification from <strong>${env.SENDER_NAME}</strong></p>
                  <p>Deployed at ${new Date().format('EEEE, MMMM dd, yyyy hh:mm a z')}</p>
                </div>
              </div>
            </body>
            </html>
          """,
          to: env.EMAIL_RECIPIENTS,
          from: "\"${env.SENDER_NAME}\" <lamphukumar228@gmail.com>",
          // replyTo: env.REPLY_TO,
          mimeType: 'text/html',
          attachLog: false
        )
        
        // Send CC if configured
        if (env.CC_LIST && env.CC_LIST != '') {
          emailext (
            subject: "✅ SUCCESS: OmniStudio Deployment - Build #${env.BUILD_NUMBER} [CC]",
            body: """
              <html>
              <body style="font-family: Arial;">
                <p>This is a CC notification of a successful OmniStudio deployment.</p>
                <p><strong>Build #${env.BUILD_NUMBER}</strong> completed successfully.</p>
                <p><a href="${env.BUILD_URL}">View Details</a></p>
              </body>
              </html>
            """,
            to: env.CC_LIST,
            from: "\"${env.SENDER_NAME}\" <lamphukumar228@gmail.com>",
            mimeType: 'text/html'
          )
        }
        
        echo '✅ Success notification sent successfully!'
      }
    }
    
    failure {
      script {
        echo "📧 Sending failure notification to: ${env.EMAIL_RECIPIENTS}"
        
        emailext (
          subject: "❌ FAILED: OmniStudio Deployment - Build #${env.BUILD_NUMBER}",
          body: """
            <html>
            <head>
              <style>
                body { font-family: Arial, sans-serif; margin: 0; padding: 0; }
                .header { background-color: #f44336; padding: 20px; color: white; }
                .content { padding: 20px; background-color: #f5f5f5; }
                .info-box { background-color: white; padding: 20px; border-radius: 5px; margin-bottom: 20px; }
                table { border-collapse: collapse; width: 100%; }
                td { padding: 10px; border: 1px solid #ddd; }
                .label { background-color: #f9f9f9; font-weight: bold; }
                .error-box { background-color: #ffebee; padding: 15px; border-left: 4px solid #f44336; 
                            margin: 20px 0; }
                .button { background-color: #f44336; color: white; padding: 12px 25px; 
                         text-decoration: none; border-radius: 5px; display: inline-block; 
                         margin: 5px; font-size: 14px; }
                .footer { text-align: center; margin-top: 20px; color: #888; font-size: 12px; }
                h2, h3 { margin-top: 0; }
              </style>
            </head>
            <body>
              <div class="header">
                <h2>❌ Deployment Failed!</h2>
              </div>
              <div class="content">
                <div class="info-box">
                  <h3 style="color: #333; border-bottom: 2px solid #f44336; padding-bottom: 10px;">
                    Build Information
                  </h3>
                  <table>
                    <tr>
                      <td class="label">Build Number:</td>
                      <td>#${env.BUILD_NUMBER}</td>
                    </tr>
                    <tr>
                      <td class="label">Job Name:</td>
                      <td>${env.JOB_NAME}</td>
                    </tr>
                    <tr>
                      <td class="label">Failed At:</td>
                      <td>${new Date().format('yyyy-MM-dd HH:mm:ss z')}</td>
                    </tr>
                    <tr>
                      <td class="label">Target Org:</td>
                      <td>TargetOrg (${env.SF_INSTANCE_URL})</td>
                    </tr>
                  </table>
                </div>
                
                <div class="error-box">
                  <h3 style="color: #c62828; margin-top: 0;">⚠️ Action Required</h3>
                  <p style="color: #555; margin: 0;">
                    The deployment has failed. Please check the console logs for detailed error information 
                    and take appropriate corrective action.
                  </p>
                </div>
                
                <div style="text-align: center; margin-top: 30px;">
                  <a href="${env.BUILD_URL}console" class="button">View Console Logs</a>
                  <a href="${env.BUILD_URL}" class="button" style="background-color: #757575;">View Build Details</a>
                </div>
                
                <div class="footer">
                  <p>This is an automated notification from <strong>${env.SENDER_NAME}</strong></p>
                  <p>Build failed at ${new Date().format('EEEE, MMMM dd, yyyy hh:mm a z')}</p>
                </div>
              </div>
            </body>
            </html>
          """,
          to: env.EMAIL_RECIPIENTS,
          from: "\"${env.SENDER_NAME}\" <lamphukumar228@gmail.com>",
          // replyTo: env.REPLY_TO,
          mimeType: 'text/html',
          attachLog: true
        )
        
        // Send CC if configured
        if (env.CC_LIST && env.CC_LIST != '') {
          emailext (
            subject: "❌ FAILED: OmniStudio Deployment - Build #${env.BUILD_NUMBER} [CC]",
            body: """
              <html>
              <body style="font-family: Arial;">
                <p style="color: #f44336;"><strong>⚠️ Deployment Failed</strong></p>
                <p>Build #${env.BUILD_NUMBER} has failed.</p>
                <p><a href="${env.BUILD_URL}console">View Console Logs</a></p>
              </body>
              </html>
            """,
            to: env.CC_LIST,
            from: "\"${env.SENDER_NAME}\" <lamphukumar228@gmail.com>",
            mimeType: 'text/html'
          )
        }
        
        echo '📧 Failure notification sent successfully!'
      }
    }
  }
}
#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG='vinay.aher@cloudsynapps.com.acamsfull'
    def SFDC_HOST = 'https://test.salesforce.com'
    def JWT_KEY_CRED_ID = '0eb7b2b0-e4b2-4833-8f0d-0f748cd8e7c0'
        //def JWT_KEY_CRED_ID = 'bc304e92-c856-49ff-a281-4a78b37e5939'

	def CONNECTED_APP_CONSUMER_KEY='3MVG9BJjUUIJZf1xAQJMjfDTKr2FYLU3hyEx9ObFmZLgZjJG_pRhSO4aPi6xFdY1TbIV.74LlsnPXUjsWIkf4'

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = env.toolbelt
	println toolbelt
    
      
    
        
//    stage('install'){
// 			     bat  "${toolbelt} plugins:install sfdx-git-delta "
//             }
        
   
//   stage('generate xml file') {
// 			   rmsg = bat returnStdout: true, script: "${toolbelt} sgd:source:delta --to "6c837ac60ab5299881904eab755b7b51c89f642b" --from "077419f43a883033cd37b10fdace43becccf0bce" --output  ./manifest ".""
//              println rmsg}
     stage('checkout source') {
//         // when running in multi-branch job, one must issue this command
         checkout scm
	     bat "${toolbelt}/sfdx -v"
	     bat "npm -v"
	     bat "node -v"
     }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username  force:source:deploy -x path/to/package.xml
			if (isUnix()) {
				//rmsg = sh returnStdout: true, script: "${toolbelt} force:source:deploy --checkonly -d manifest/. -u ${HUB_ORG}"
			}else{
			   //rmsg = bat returnStdout: true, script: "${toolbelt}  force:source:deploy --checkonly -x manifest/package.xml  -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }

}




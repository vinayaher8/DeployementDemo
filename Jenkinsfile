#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = env.toolbelt
	println toolbelt
    
script {
              
                    def confirm = input message: '? This plugin is not digitally signed and its authenticity cannot be verified. 
Continue installation? ', ok: 'Proceed', parameters: [choice(choices: 'y\N', description: 'yes/no', name: 'confirm')], defaultValue: 'y'
                }
                if (confirm == "y") {
                    			     bat  "${toolbelt} plugins:install sfdx-git-delta "

                } else {
                    echo "Aborting..."
                }
            }

    
   stage('install'){
			     bat  "${toolbelt} plugins:install sfdx-git-delta "
            }
        
   
  stage('generate xml file') {
			   rmsg = bat returnStdout: true, script: "${toolbelt} sgd:source:delta --to "6c837ac60ab5299881904eab755b7b51c89f642b" --from "077419f43a883033cd37b10fdace43becccf0bce" --output  ./manifest ".""
             println rmsg}
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
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
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "${toolbelt}  force:source:deploy -x manifest/package/package.xml --postdestructivechanges manifest/destructiveChanges/destructiveChanges.xml -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }

}




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
  stage('Create Delta Package') {
            steps {
                sfdx sgd:source:delta --to "HEAD" --from "HEAD~1" --output "."
            }
        }
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
			   rmsg = bat returnStdout: true, script: "${toolbelt}  force:source:deploy:delta -x manifest/package/package.xml --postdestructivechanges manifest/destructiveChanges/destructiveChanges.xml -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }

}


pipeline {
    agent any
    stages {
        stage('Authenticate with Salesforce') {
            steps {
                sfdx force:auth:jwt:grant --clientid 3MVG9fe4g9fhX0E5nE8CDuBIKALv9M8ZFWxS4hqVRRicDiIQFUF1frNNszYW5fug4TXD3hxOT2IwKYVJhb40_ --username vinay.aher@cloudsynapps.com --jwtkeyfile "****" --setdefaultdevhubusername --instanceurl https://login.salesforce.com
            }
        }
        stage('Create Delta Package') {
            steps {
                sfdx sgd:source:delta --to "HEAD" --from "HEAD~1" --output "."
            }
        }
        stage('Deploy Changes') {
            steps {
                def result = sfdx force:source:deploy:delta --json --loglevel fatal -u vinay.aher@cloudsynapps.com -p /manifest/package/package.xml
                echo "Deployment Results: ${result}"
                if (result.status == 0) {
                    echo "Deployment Successful"
                } else {
                    error("Deployment Failed: ${result.result.error}")
                }
            }
        }
    }
}

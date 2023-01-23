
pipeline {
    agent any
    stages {
        stage('Authenticate with Salesforce') {
            steps {
                sfdx force:auth:jwt:grant --clientid 3MVG9fe4g9fhX0E5nE8CDuBIKALv9M8ZFWxS4hqVRRicDiIQFUF1frNNszYW5fug4TXD3hxOT2IwKYVJhb40_ --username vinay.aher@cloudsynapps.com --jwtkeyfile 0eb7b2b0-e4b2-4833-8f0d-0f748cd8e7c0 --instanceurl https://login.salesforce.com
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

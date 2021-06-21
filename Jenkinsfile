pipeline {
    agent any

    stages {
		stage('Get Image Tag'){
			steps{
			sh "echo ${BUILD_ID}"
			sh "sed  -i /tag/c\\ tag=${BUILD_ID} .env"
			}
		}
		stage('Deploy Development cluster'){
				steps{
					sh './docker-compose.sh'
				}
		}
	}
	post{
		success {
			script{
				def token = sh(returnStdout:true, script: 'cat token.txt').trim()
				def response = httpRequest url: 'https://lqe18031.live.dynatrace.com/api/v1/events', acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', customHeaders: [[name: 'Authorization', value: "$token"]], httpMode: 'POST', requestBody: """{ 
					"eventType": "CUSTOM_DEPLOYMENT", 
					"attachRules": { 
						"entityIds": [ 
							"HOST-A095FBF9C49D7AA7" 
						], 
						"tagRule" : { 
							"meTypes" : "HOST", 
							"tags" : "Dev" 
						} 
					}, 
					"deploymentName":"${JOB_NAME}", 
					"deploymentVersion":"${BUILD_ID}",
					"source":"Jenkins",
					"customProperties":{ 
						"Image Version" : "${BUILD_ID}",
						"Pipeline Name" : "${JOB_NAME}",
						"Deployment Status" : "Success"
					} 
				}"""
			}	
		}
		failure {
			script{
				def token = sh(returnStdout:true, script: 'cat token.txt').trim()
				def response = httpRequest url: 'https://lqe18031.live.dynatrace.com/api/v1/events', acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', customHeaders: [[name: 'Authorization', value: "$token"]], httpMode: 'POST', requestBody: """{ 
					"eventType": "CUSTOM_DEPLOYMENT", 
					"attachRules": { 
						"entityIds": [ 
							"HOST-A095FBF9C49D7AA7" 
						], 
						"tagRule" : { 
							"meTypes" : "HOST", 
							"tags" : "Dev" 
						} 
					}, 
					"deploymentName":"${JOB_NAME}", 
					"deploymentVersion":"${BUILD_ID}",
					"source":"Jenkins",
					"customProperties":{ 
						"Image Version" : "${BUILD_ID}",
						"Pipeline Name" : "${JOB_NAME}",
						"Deployment Status" : "Failed"
					} 
				}"""
			}
		}

	}
}

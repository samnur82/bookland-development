pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
		stage('Deploy cluster'){
            steps{
                sh './docker-compose.sh'
            }
        }
        stage('Push event to dynatrace'){
            steps{
                script{
                    def response = httpRequest url: 'https://lqe18031.live.dynatrace.com/api/v1/events', acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', customHeaders: [[name: 'Authorization', value: 'Api-Token dt0c01.75CH7KCEDEZ2AFRKSOIFQ66P.BAV75OBRSOORESSJUDUANEIPWOYJGKN2WKTU4ZOQRAOLONQOOF3RN76IXZZJCD57']], httpMode: 'POST', requestBody: """{ 
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
						"deploymentVersion":"0.${BUILD_ID}", 
						"deploymentProject":"BookLandServices", 
						"remediationAction":"http://revertMe", 
						"ciBackLink":"${BUILD_URL}", 
						"source":"Jenkins",
						"customProperties":{ 
							"Jenkins Build Number": "${BUILD_ID}" 
						} 
					}"""
                    println('Status: '+response.status)
                    println('Response: '+response.content)
                }
            }
        }
    }
}

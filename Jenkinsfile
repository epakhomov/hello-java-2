pipeline {
	agent any


	stages {


		stage('Black Duck pull request scan') {
			when {
				allOf {
					changeRequest()
					expression { CHANGE_TARGET ==~ /(master|stage|release)/ }
				}
			}
			steps {
				
					sh '''
                        echo 'RUNNING DETECT'
			bash <(curl -s -L https://detect.synopsys.com/detect.sh) --blackduck.url=https://poc06.blackduck.synopsys.com --blackduck.api.token=MWI0ZjBjZmYtZGU5OS00ODJjLWEzNzYtMGFjZDMyOGVmOWUzOmY4N2MzMDNmLTE1NzEtNDE2My1hN2JlLWVkZTg1YjgzNWQxOQ== --detect.project.name=test
		
						
					'''
				
			}
		}

	}
	post {
		always {
			cleanWs()
		}
	}
}

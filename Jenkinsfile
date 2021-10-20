pipeline {
	agent any

	stages {
		stage('Build') {
			steps {
				sh 'mvn clean compile'
			}
		}
		stage('Black Duck Scan') {
			when {
				allOf {
					changeRequest()
					expression { CHANGE_TARGET ==~ /(master|stage|release)/ }
				}
			}
			steps {
				withCoverityEnvironment(coverityInstanceUrl: "$CONNECT", projectName: "$PROJECT", streamName: "$PROJECT") {
					sh '''
						export CHANGE_SET=$(git --no-pager diff origin/$CHANGE_TARGET --name-only)
						[ -z "$CHANGE_SET" ] && exit 0
					'''
                    synopsys_detect ''' bash <(curl -s -L https://detect.synopsys.com/detect.sh) --blackduck.url=https://poc06.blackduck.synopsys.com --blackduck.api.token=MWI0ZjBjZmYtZGU5OS00ODJjLWEzNzYtMGFjZDMyOGVmOWUzOmY4N2MzMDNmLTE1NzEtNDE2My1hN2JlLWVkZTg1YjgzNWQxOQ== --detect.project.name=test --logging.level.detect=DEBUG'''
				}

			}
		}

	}
	post {
		always {
			cleanWs()
		}
	}
}



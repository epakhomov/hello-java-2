pipeline {
	agent any

	environment {
		CONNECT = 'http://ec2-18-144-82-126.us-west-1.compute.amazonaws.com:8888'
		PROJECT = 'hello-java'
	}

	stages {
		stage('Build') {
			steps {
				sh 'mvn clean compile'
			}
		}

		stage('Coverity Full Scan') {
			when {
				allOf {
					not { changeRequest() }
					expression { BRANCH_NAME ==~ /(master|stage|release)/ }
				}
			}
			steps {
				withCoverityEnvironment(coverityInstanceUrl: "$CONNECT", projectName: "$PROJECT", streamName: "$PROJECT") {
					sh '''
						cov-build --dir idir mvn clean compile
						cov-analyze --dir idir
						cov-commit-defects --dir idir  --url $COV_URL --stream $PROJECT 
					'''
					script { // Coverity Quality Gate
						count = coverityIssueCheck(viewName: 'Outstanding Issues', returnIssueCount: true)
						if (count != 0) { unstable 'issues detected' }
					}
				}
			}
		}
		stage('Coverity Incremental Scan') {
			when {
				allOf {
					changeRequest()
					expression { CHANGE_TARGET ==~ /(master|stage|release)/ }
				}
			}
			steps {

					sh '''
						export CHANGE_SET=$(git --no-pager diff origin/$CHANGE_TARGET --name-only)
						[ -z "$CHANGE_SET" ] && exit 0
						
					'''
				synopsys_detect ''' bash <(curl -s -L https://detect.synopsys.com/detect.sh) --blackduck.url=https://poc06.blackduck.synopsys.com --blackduck.api.token=MWI0ZjBjZmYtZGU5OS00ODJjLWEzNzYtMGFjZDMyOGVmOWUzOmY4N2MzMDNmLTE1NzEtNDE2My1hN2JlLWVkZTg1YjgzNWQxOQ== --detect.project.name=test '''

			}
		}

	}
	post {
		always {
			cleanWs()
		}
	}
}

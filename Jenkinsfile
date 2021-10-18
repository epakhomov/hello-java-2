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
				withCoverityEnvironment(coverityInstanceUrl: "$CONNECT", projectName: "$PROJECT", streamName: "$PROJECT-$CHANGE_TARGET") {
					sh '''
						export CHANGE_SET=$(git --no-pager diff origin/$CHANGE_TARGET --name-only)
						[ -z "$CHANGE_SET" ] && exit 0
						cov-run-desktop --dir idir --url $COV_URL --stream $COV_STREAM --build mvn clean compile
						cov-run-desktop --dir idir --url $COV_URL --stream $COV_STREAM --present-in-reference false \
							--ignore-uncapturable-inputs true --text-output issues.txt $CHANGE_SET
						if [ -s issues.txt ]; then cat issues.txt; touch issues_found; fi
					'''
				}
				script { // Coverity Quality Gate
					if (fileExists('issues_found')) { unstable 'issues detected' }
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

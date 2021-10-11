pipeline {
	agent any

	environment {
		CONNECT = 'http://localhost:8888'
		PROJECT = 'hello-java'
	}

	stages {
		stage('Build') {
			steps {
				sh 'mvn -B compile'
			}
		}
		stage('Test') {
			steps {
				sh 'mvn -B test'
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
				withCoverityEnvironment(coverityInstanceUrl: "$CONNECT", projectName: "$PROJECT", streamName: "$PROJECT-$BRANCH_NAME") {
					sh '''
						cov-build --dir idir mvn clean compile
						cov-analyze --dir idir --ticker-mode none --strip-path $WORKSPACE --webapp-security
						cov-commit-defects --dir idir --ticker-mode none --url http://localhost:8888 --stream hello-java \
							--description $BUILD_TAG --target Linux_x86_64 --version $GIT_COMMIT
					'''
					script { // Coverity Quality Gate
						count = coverityIssueCheck(viewName: 'Outstanding Issues', returnIssueCount: true)
						if (count != 0) { unstable 'issues detected' }
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

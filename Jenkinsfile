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
			export CHANGE_SET=$(git --no-pager diff origin/$CHANGE_TARGET --name-only)
						[ -z "$CHANGE_SET" ] && exit 0
						
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

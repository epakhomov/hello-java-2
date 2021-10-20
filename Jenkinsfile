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
		
					sh '''
						export CHANGE_SET=$(git --no-pager diff origin/$CHANGE_TARGET --name-only)
						[ -z "$CHANGE_SET" ] && exit 0
					'''
                    synopsys_detect "--detect.project.name=test"
				

			}
		}

	}
	post {
		always {
			cleanWs()
		}
	}
}



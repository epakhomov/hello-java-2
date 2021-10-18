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

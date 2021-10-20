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

                    synopsys_detect "--detect.project.name=test --logging.level.com.synopsys.integration=DEBUG"
				

			}
		}

	}
	post {
		always {
			cleanWs()
		}
	}
}



pipeline {
	agent any 
	stages { 
		stage('Run automated tests') { 
			steps { 
				echo 'Run automated tests' 
			} 
		} 
		stage('SonarQube analysis') {
			steps {
                script {
                    scannerHome = tool 'sonar-scanner';
                }
                withSonarQubeEnv('SonarQube') { // Replace this name by the one you setup in the SonarQube Jenkins configuration (step 2)
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
		} 
		stage('JMeter Test') {
            steps {
                script {
                    // Path to the JMeter installation directory
                    def jmeterHome = '/usr/share/apache-jmeter-5.5'

                    // Path to the JMeter test script
                    def jmeterScript = './jmeter-jenkins.jmx'

                    // Execute JMeter test
                    sh "${jmeterHome}/bin/jmeter -n -t ${jmeterScript} -l result.jtl"
                }
            }
            post {
                always {
                    // Archive JTL result file
                    archiveArtifacts 'result.jtl'
                }
                success {
                    // Publish JMeter report using Performance plugin
                    perfReport filterRegex:'', sourceDataFiles: 'result.jtl'
                }
            }
        }
	stage('Perform manual testing') { 
		steps {
	                // One or more steps need to be included within the steps block.
	                timeout(time: 1, unit: 'HOURS') {
	                    input 'Proceed to production?'
                }
            } 
	} 
}
}

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
                    def jmeterHome = '/usr/share/jmeter'

                    // Path to the JMeter test script
                    def jmeterScript = './testPlanHome.jmx'

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
				echo 'Perform manual testing'
                timeout(time: 2, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
			} 
		} 
	} 
}

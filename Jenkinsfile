pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\ApacheJMeter'
    }

    stages {

        stage('Run JMeter') {
            steps {
                bat """
                %JMETER_HOME%\\bin\\jmeter.bat ^
                -n ^
                -t Dialysis_10000_DataCreationScript_11_06_2026.jmx ^
                -l results.jtl ^
                -e ^
                -o HTMLReport
                """
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'results.jtl'
            archiveArtifacts artifacts: 'HTMLReport/**'
        }
    }
}

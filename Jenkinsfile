pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\admin\\OneDrive\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
    }

    stages {

        stage('Verify Environment') {
            steps {
                bat '''
                echo =====================================
                echo Current Directory
                echo =====================================
                cd

                echo.
                echo =====================================
                echo Workspace Files
                echo =====================================
                dir

                echo.
                echo =====================================
                echo JMeter Version
                echo =====================================
                "%JMETER_HOME%\\bin\\jmeter.bat" -v
                '''
            }
        }

        stage('Run JMeter') {
            steps {
                bat '''
                "%JMETER_HOME%\\bin\\jmeter.bat" -n -t "Dialysis_10000_DataCreationScript_11_06_2026.jmx" -l results.jtl -e -o HTMLReport
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'results.jtl', allowEmptyArchive: true
            archiveArtifacts artifacts: 'HTMLReport/**', allowEmptyArchive: true
        }
    }
}

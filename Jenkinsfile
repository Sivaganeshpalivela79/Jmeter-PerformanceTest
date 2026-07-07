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

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo =====================================
                echo Running JMeter Test
                echo =====================================

                echo Workspace: %WORKSPACE%

                dir "%WORKSPACE%"

                "%JMETER_HOME%\\bin\\jmeter.bat" -n ^
                -t "%WORKSPACE%\\Dialysis_10000_DataCreationScript_11_06_2026.jmx" ^
                -l "%WORKSPACE%\\results.jtl" ^
                -e ^
                -o "%WORKSPACE%\\HTMLReport"
                '''
            }
        }
    }

    post {
        always {

            echo "Archiving JMeter Results..."

            archiveArtifacts artifacts: 'results.jtl', allowEmptyArchive: true
            archiveArtifacts artifacts: 'HTMLReport/**', allowEmptyArchive: true

            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'HTMLReport',
                reportFiles: 'index.html',
                reportName: 'JMeter HTML Report'
            ])

            echo "HTML Report Published Successfully"
        }

        success {
            echo "====================================="
            echo "JMeter Test Execution SUCCESS"
            echo "====================================="
        }

        failure {
            echo "====================================="
            echo "JMeter Test Execution FAILED"
            echo "====================================="
        }
    }
}

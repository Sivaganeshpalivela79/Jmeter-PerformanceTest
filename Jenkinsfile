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

        stage('Clean Previous Reports') {
            steps {
                bat '''
                echo =====================================
                echo Cleaning Previous Reports
                echo =====================================

                if exist "%WORKSPACE%\\HTMLReport" (
                    echo Deleting old HTMLReport folder...
                    rmdir /S /Q "%WORKSPACE%\\HTMLReport"
                )

                if exist "%WORKSPACE%\\results.jtl" (
                    echo Deleting old results.jtl...
                    del /Q "%WORKSPACE%\\results.jtl"
                )

                if exist "%WORKSPACE%\\jmeter.log" (
                    echo Deleting old jmeter.log...
                    del /Q "%WORKSPACE%\\jmeter.log"
                )
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

                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                -n ^
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

            echo "====================================="
            echo "Archiving JMeter Results"
            echo "====================================="

            archiveArtifacts artifacts: 'results.jtl', allowEmptyArchive: true
            archiveArtifacts artifacts: 'jmeter.log', allowEmptyArchive: true
            archiveArtifacts artifacts: 'HTMLReport/**', allowEmptyArchive: true

            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'HTMLReport',
                reportFiles: 'index.html',
                reportName: 'JMeter HTML Report'
            ])
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

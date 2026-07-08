pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\admin\\OneDrive\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
        SCRIPT_PATH = 'JMeterScripts\\Dialysis_10000_DataCreationScript_11_06_2026.jmx'
        RESULTS_DIR = 'reports'
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Verify Environment') {
            steps {
                bat '''
                echo =========================================
                echo VERIFY ENVIRONMENT
                echo =========================================

                echo Workspace:
                cd

                echo.
                echo Repository Files
                dir

                echo.
                echo JMeter Scripts
                dir JMeterScripts

                echo.
                echo Reports Folder
                dir reports

                echo.
                echo JMeter Version
                "%JMETER_HOME%\\bin\\jmeter.bat" -v
                '''
            }
        }

        stage('Clean Reports') {
            steps {
                bat '''
                if exist reports\\HTMLReport (
                    rmdir /S /Q reports\\HTMLReport
                )

                if exist reports\\results.jtl (
                    del /F /Q reports\\results.jtl
                )

                if exist reports\\jmeter.log (
                    del /F /Q reports\\jmeter.log
                )
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                -n ^
                -t "%WORKSPACE%\\%SCRIPT_PATH%" ^
                -l "%WORKSPACE%\\reports\\results.jtl" ^
                -j "%WORKSPACE%\\reports\\jmeter.log" ^
                -e ^
                -o "%WORKSPACE%\\reports\\HTMLReport"
                '''
            }
        }

        stage('Verify Report') {
            steps {
                bat '''
                echo =========================================
                echo GENERATED REPORT
                echo =========================================

                dir reports

                echo.

                dir reports\\HTMLReport
                '''
            }
        }
    }

    post {

        always {

            archiveArtifacts artifacts: 'reports/**/*.jtl', fingerprint: true
            archiveArtifacts artifacts: 'reports/**/*.log', fingerprint: true
            archiveArtifacts artifacts: 'reports/HTMLReport/**', fingerprint: true

            publishHTML(target: [
                reportDir: 'reports/HTMLReport',
                reportFiles: 'index.html',
                reportName: 'JMeter HTML Report',
                keepAll: true,
                alwaysLinkToLastBuild: true,
                allowMissing: false
            ])
        }

        success {
            echo "========================================"
            echo "JMETER TEST EXECUTED SUCCESSFULLY"
            echo "========================================"
        }

        failure {
            echo "========================================"
            echo "JMETER TEST FAILED"
            echo "========================================"
        }
    }
}

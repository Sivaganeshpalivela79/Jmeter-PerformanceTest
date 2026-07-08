pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\admin\\OneDrive\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
        JMETER_TEST = 'EMP_003_SalaryReport.jmx'
        REPORT_DIR = 'HTMLReport'
        RESULTS_FILE = 'results.jtl'
        LOG_FILE = 'jmeter.log'
    }

    stages {

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Clean Workspace') {
    steps {
        bat '''
        echo Cleaning old reports...

        if exist "%WORKSPACE%\\HTMLReport" (
            rmdir /S /Q "%WORKSPACE%\\HTMLReport"
        )

        if exist "%WORKSPACE%\\results.jtl" (
            del /F /Q "%WORKSPACE%\\results.jtl"
        )

        if exist "%WORKSPACE%\\jmeter.log" (
            del /F /Q "%WORKSPACE%\\jmeter.log"
        )
        '''
    }
}

        stage('Verify JMeter') {
            steps {
                bat '''
                echo ==========================================
                echo VERIFY JMETER INSTALLATION
                echo ==========================================

                echo Workspace:
                cd

                echo.
                dir

                echo.
                "%JMETER_HOME%\\bin\\jmeter.bat" -v
                '''
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '''
                echo ==========================================
                echo RUNNING JMETER TEST
                echo ==========================================

                if exist "%WORKSPACE%\\%REPORT_DIR%" (
                    rmdir /S /Q "%WORKSPACE%\\%REPORT_DIR%"
                )

                if exist "%WORKSPACE%\\%RESULTS_FILE%" (
                    del /Q "%WORKSPACE%\\%RESULTS_FILE%"
                )

                if exist "%WORKSPACE%\\%LOG_FILE%" (
                    del /Q "%WORKSPACE%\\%LOG_FILE%"
                )

                "%JMETER_HOME%\\bin\\jmeter.bat" ^
                -n ^
                -t "%WORKSPACE%\\%JMETER_TEST%" ^
                -l "%WORKSPACE%\\%RESULTS_FILE%" ^
                -j "%WORKSPACE%\\%LOG_FILE%" ^
                -e ^
                -o "%WORKSPACE%\\%REPORT_DIR%"
                '''
            }
        }

        stage('Verify Report') {
            steps {
                bat '''
                echo ==========================================
                echo VERIFY GENERATED REPORT
                echo ==========================================

                dir "%WORKSPACE%"

                echo.
                dir "%WORKSPACE%\\%REPORT_DIR%"

                echo.
                dir "%WORKSPACE%\\%REPORT_DIR%\\content\\js"
                '''
            }
        }
    }

    post {

        always {

            echo "=========================================="
            echo "ARCHIVING REPORTS"
            echo "=========================================="

            archiveArtifacts artifacts: '**/*.jtl', fingerprint: true
            archiveArtifacts artifacts: '**/*.log', fingerprint: true
            archiveArtifacts artifacts: 'HTMLReport/**', fingerprint: true

            publishHTML([
                target: [
                    reportDir: 'HTMLReport',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false,
                    includes: '**/*',
                    escapeUnderscores: false
                ]
            ])
        }

        success {
            echo "=========================================="
            echo "JMETER EXECUTION SUCCESSFUL"
            echo "=========================================="
        }

        failure {
            echo "=========================================="
            echo "JMETER EXECUTION FAILED"
            echo "=========================================="
        }
    }
}

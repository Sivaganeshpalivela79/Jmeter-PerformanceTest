pipeline {

    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\admin\\OneDrive\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
        SCRIPT_DIR = 'JMeterScripts'
        REPORT_DIR = 'reports'
    }

   parameters {

    choice(
        name: 'TEST_SCRIPT',
        choices: [
            'ALL',
            'Dialysis_10000_DataCreationScript_11_06_2026.jmx',
            'EMP_001_SearchEmployeeById.jmx',
            'EMP_002_SearchByDepartment.jmx',
            'EMP_003_SalaryReport.jmx'
        ],
        description: 'Select JMeter Script'
    )

    string(
        name: 'THREADS',
        defaultValue: '10',
        description: 'Number of Virtual Users'
    )

    string(
        name: 'RAMP_UP',
        defaultValue: '10',
        description: 'Ramp-Up Time (seconds)'
    )

    string(
        name: 'LOOPS',
        defaultValue: '1',
        description: 'Loop Count'
    )

    string(
        name: 'DURATION',
        defaultValue: '60',
        description: 'Test Duration (seconds)'
    )

    choice(
        name: 'ENVIRONMENT',
        choices: [
            'DEV',
            'UAT',
            'PERF',
            'PROD'
        ],
        description: 'Select Target Environment'
    )

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
                echo ==========================================
                echo VERIFY ENVIRONMENT
                echo ==========================================

                echo Workspace
                cd

                echo.
                echo Repository Files
                dir

                echo.
                echo JMeter Scripts
                dir JMeterScripts

                echo.
                "%JMETER_HOME%\\bin\\jmeter.bat" -v
                '''
            }
        }

        stage('Clean Reports') {
            steps {
                bat '''
                if exist "%REPORT_DIR%" (
                    rmdir /S /Q "%REPORT_DIR%"
                )

                mkdir "%REPORT_DIR%"
                '''
            }
        }

        stage('Run JMeter Tests') {

    steps {

        script {

            if (params.TEST_SCRIPT == "ALL") {

                parallel(

                    "Dialysis": {

                        bat """
                        "%JMETER_HOME%/bin/jmeter.bat" ^
                        -n ^
                        -t "%WORKSPACE%\${SCRIPT_DIR}\Dialysis_10000_DataCreationScript_11_06_2026.jmx" ^
                        -l "%WORKSPACE%\${REPORT_DIR}\Dialysis.jtl" ^
                        -j "%WORKSPACE%\${REPORT_DIR}\Dialysis.log" ^
                        -Jthreads=${params.THREADS} ^
                        -Jrampup=${params.RAMP_UP} ^
                        -Jloops=${params.LOOPS} ^
                        -Jenv=${params.ENVIRONMENT} ^
                        -e ^
                        -o "%WORKSPACE%\${REPORT_DIR}\Dialysis"
                        """
                    },

                    "EMP001": {

                        bat """
                        "%JMETER_HOME%/bin/jmeter.bat" ^
                        -n ^
                        -t "%WORKSPACE%\${SCRIPT_DIR}\EMP_001_SearchEmployeeById.jmx" ^
                        -l "%WORKSPACE%\${REPORT_DIR}\EMP001.jtl" ^
                        -j "%WORKSPACE%\${REPORT_DIR}\EMP001.log" ^
                        -Jthreads=${params.THREADS} ^
                        -Jrampup=${params.RAMP_UP} ^
                        -Jloops=${params.LOOPS} ^
                        -Jenv=${params.ENVIRONMENT} ^
                        -e ^
                        -o "%WORKSPACE%\${REPORT_DIR}\EMP001"
                        """
                    },

                    "EMP002": {

                        bat """
                        "%JMETER_HOME%/bin/jmeter.bat" ^
                        -n ^
                        -t "%WORKSPACE%\${SCRIPT_DIR}\EMP_002_SearchByDepartment.jmx" ^
                        -l "%WORKSPACE%\${REPORT_DIR}\EMP002.jtl" ^
                        -j "%WORKSPACE%\${REPORT_DIR}\EMP002.log" ^
                        -Jthreads=${params.THREADS} ^
                        -Jrampup=${params.RAMP_UP} ^
                        -Jloops=${params.LOOPS} ^
                        -Jenv=${params.ENVIRONMENT} ^
                        -e ^
                        -o "%WORKSPACE%\${REPORT_DIR}\EMP002"
                        """
                    },

                    "EMP003": {

                        bat """
                        "%JMETER_HOME%/bin/jmeter.bat" ^
                        -n ^
                        -t "%WORKSPACE%\${SCRIPT_DIR}\EMP_003_SalaryReport.jmx" ^
                        -l "%WORKSPACE%\${REPORT_DIR}\EMP003.jtl" ^
                        -j "%WORKSPACE%\${REPORT_DIR}\EMP003.log" ^
                        -Jthreads=${params.THREADS} ^
                        -Jrampup=${params.RAMP_UP} ^
                        -Jloops=${params.LOOPS} ^
                        -Jenv=${params.ENVIRONMENT} ^
                        -e ^
                        -o "%WORKSPACE%\${REPORT_DIR}\EMP003"
                        """
                    }

                )

            } else {

                def reportName = params.TEST_SCRIPT.replace(".jmx","")

                bat """
               "%JMETER_HOME%/bin/jmeter.bat" ^
                -n ^
                -t "%WORKSPACE%\${SCRIPT_DIR}\${params.TEST_SCRIPT}" ^
                -l "%WORKSPACE%\${REPORT_DIR}\${reportName}.jtl" ^
                -j "%WORKSPACE%\${REPORT_DIR}\${reportName}.log" ^
                -Jthreads=${params.THREADS} ^
                -Jrampup=${params.RAMP_UP} ^
                -Jloops=${params.LOOPS} ^
                -Jenv=${params.ENVIRONMENT} ^
                -e ^
                -o "%WORKSPACE%\${REPORT_DIR}\${reportName}"
                """
            }

        }

    }

}
        stage('Verify Reports') {

            steps {

                bat '''
                echo ==========================================
                echo GENERATED REPORTS
                echo ==========================================

                dir reports
                '''

            }

        }

    }

    post {

        always {

            echo "====================================="
            echo "ARCHIVING REPORTS"
            echo "====================================="

            archiveArtifacts(
                artifacts: 'reports/**/*.jtl',
                allowEmptyArchive: true,
                fingerprint: true
            )

            archiveArtifacts(
                artifacts: 'reports/**/*.log',
                allowEmptyArchive: true,
                fingerprint: true
            )

            archiveArtifacts(
                artifacts: 'reports/**',
                allowEmptyArchive: true,
                fingerprint: true
            )

            publishHTML(target: [
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports',
                reportFiles: 'index.html',
                reportName: 'JMeter HTML Reports'
            ])

            perfReport(
                sourceDataFiles: 'reports/*.jtl',
                filterRegex: '',
                showTrendGraphs: true
            )

        }

        success {

            echo "====================================="
            echo "JMeter Execution Completed Successfully"
            echo "====================================="

        }

        failure {

            echo "====================================="
            echo "JMeter Execution Failed"
            echo "====================================="

        }

    }

}

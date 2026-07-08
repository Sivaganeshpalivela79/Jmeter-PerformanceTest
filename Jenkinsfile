pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\Users\\admin\\OneDrive\\Documents\\apache-jmeter-5.6.3\\apache-jmeter-5.6.3'
        SCRIPT_DIR = 'JMeterScripts'
        REPORT_DIR = 'reports'
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

                echo Workspace:
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

                        def scripts = [
                            "Dialysis_10000_DataCreationScript_11_06_2026.jmx",
                            "EMP_001_SearchEmployeeById.jmx",
                            "EMP_002_SearchByDepartment.jmx",
                            "EMP_003_SalaryReport.jmx"
                        ]

                        for (s in scripts) {

                            def reportName = s.replace(".jmx","")

                            bat """
                            echo ==========================================
                            echo Running ${reportName}
                            echo ==========================================

                            "%JMETER_HOME%\\\\bin\\\\jmeter.bat" ^
                            -n ^
                            -t "%WORKSPACE%\\\\${SCRIPT_DIR}\\\\${s}" ^
                            -l "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}.jtl" ^
                            -j "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}.log" ^
                            -e ^
                            -o "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}"
                            """
                        }

                    } else {

                        def reportName = params.TEST_SCRIPT.replace(".jmx","")

                        bat """
                        echo ==========================================
                        echo Running ${reportName}
                        echo ==========================================

                        "%JMETER_HOME%\\\\bin\\\\jmeter.bat" ^
                        -n ^
                        -t "%WORKSPACE%\\\\${SCRIPT_DIR}\\\\${params.TEST_SCRIPT}" ^
                        -l "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}.jtl" ^
                        -j "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}.log" ^
                        -e ^
                        -o "%WORKSPACE%\\\\${REPORT_DIR}\\\\${reportName}"
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

            archiveArtifacts artifacts: 'reports/**/*.jtl', fingerprint: true
            archiveArtifacts artifacts: 'reports/**/*.log', fingerprint: true
            archiveArtifacts artifacts: 'reports/**', fingerprint: true

            publishHTML(target: [
                reportDir: 'reports',
                reportFiles: '**/index.html',
                reportName: 'JMeter HTML Reports',
                keepAll: true,
                alwaysLinkToLastBuild: true,
                allowMissing: true
            ])
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

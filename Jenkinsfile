pipeline {
    agent any
    triggers {
        pollSCM 'H/10 * * * *'
    }
    tools {
        gradle 'Default'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/rgaryburgess/lv-test.git', branch: 'master'
            }
        }
        stage('Build') {
            steps {
                  sh 'gradle clean build -Pbuildver=${JOB_NAME}-${BUILD_NUMBER}'
            }
        }
    }
    post {
        always {
            junit testResults: 'build/test-results/test/TEST-*.xml'
            recordIssues enabledForFailure: true, aggregatingResults: true, tool: checkStyle(pattern: 'build/reports/checkstyle/*.xml')
            recordIssues enabledForFailure: true, aggregatingResults: true, tool: pmdParser(pattern: 'build/reports/pmd/*.xml')
            recordIssues enabledForFailure: true, aggregatingResults: true, tool: findBugs(pattern: 'build/reports/findbugs/*.xml')
        }
        failure {
            mail(to: 'r.gary.burgess@rainbowtag.co.uk', subject: "Failed Pipeline", body: "Failure")
        }
        success {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true
        }
    }
}
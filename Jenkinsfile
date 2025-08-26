// example Jenkinsfile for Black Duck scans using the Black Duck Security Scan Plugin
// https://plugins.jenkins.io/blackduck-security-scan

def getProjectVersion() {
    return env.PRSCAN == 'true' ? env.CHANGE_TARGET : env.BRANCH_NAME
}

pipeline {
    agent any
    environment {
        REPOSITORY_NAME = "${env.GIT_URL.tokenize('/.')[-2]}"
        FULLSCAN = "${env.BRANCH_NAME ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
        PRSCAN = "${env.CHANGE_TARGET ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
        DETECT_PROJECT_NAME = "${env.REPOSITORY_NAME}"
    }
    stages {
        stage('Black Duck SCA') {
            when {
                anyOf {
                    environment name: 'FULLSCAN', value: 'true'
                    environment name: 'PRSCAN', value: 'true'
                }
            }
            steps {
                script {
                    def version = getProjectVersion()
                    
                    security_scan product: 'blackducksca',
                        blackducksca_prComment_enabled: true,
                        blackducksca_reports_sarif_create: true,
                        detect_args: "--detect.project.name='${env.DETECT_PROJECT_NAME}' --detect.project.version.name=${version}",
                        detect_search_depth: 20,
                        //mark_build_status: 'UNSTABLE',
                        include_diagnostics: false
                }
            }
        }
    }
    post {
        always {
            archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/bridge.log'
            // zip zipFile: 'bridge-logs.zip', archive: true, dir: '.bridge'
            cleanWs()
        }
    }
}

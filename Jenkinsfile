// Coverity - SAST:
pipeline {
    agent { label 'linux64' }
    tools {
        maven 'maven-3.9'
        jdk 'openjdk-23'
    }
    stages {
        stage('Build') {
            steps {
                sh 'java -version',
                sh './mvnw install -DskipTests'
            }
        } stage('Coverity') {
            /*when {
                anyOf {
                    environment name: 'FULLSCAN', value: 'true'
                    environment name: 'PRSCAN', value: 'true'
                }
            }*/
            steps {
                security_scan product: 'coverity',
                    coverity_project_name: "webgoat",
                    coverity_stream_name: "webgoat",
                    // Uncomment the coverity_local line below if using traditional Coverity deployments or 
                    // Cloud Native Coverity (CNC) with scan services disabled
                    coverity_local: true,
                    coverity_policy_view: 'Outstanding Issues',
                    coverity_prComment_enabled: true,
                    //mark_build_status: 'UNSTABLE',
                    include_diagnostics: false,
                    network_ssl_trustAll: true
            }
        }  
    } post {
       always {
           archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/bridge.log, .bridge/*/idir/build-log.txt'
           // zip archive: true, dir: '.bridge', zipFile: 'bridge-logs.zip'
           cleanWs()
       }
    }
}

// BLACK DUCK SCA:
/* def getProjectVersion() {
    return env.PRSCAN == 'true' ? env.CHANGE_TARGET : env.BRANCH_NAME
}

pipeline {
    agent any
    environment {
        REPOSITORY_NAME = "${env.GIT_URL.tokenize('/.')[-2]}"
        FULLSCAN = "${env.BRANCH_NAME ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
        PRSCAN = "${env.CHANGE_TARGET ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
        DETECT_PROJECT_NAME = "${env.REPOSITORY_NAME}"
        DETECT_EXCLUDED_DETECTOR_TYPES="GIT"
    }
    stages {
        stage('Black Duck SCA') {
            /*when {
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
*/

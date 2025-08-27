pipeline {
    agent any
    tools {
        maven 'maven-3.9'
        jdk 'openjdk-17'
    }
    environment {
        REPOSITORY_NAME = "${env.GIT_URL.tokenize('/.')[-2]}"
        FULLSCAN = "${env.BRANCH_NAME ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
        PRSCAN = "${env.CHANGE_TARGET ==~ /^(main|master|develop|stage|release)$/ ? 'true' : 'false'}"
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    export JAVA_HOME=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17
                    export PATH=$JAVA_HOME/bin:$PATH
                    
                    mvn spotless:apply
                    mvn -N wrapper:wrapper
                    chmod +x ./mvnw
                '''
            }
        }
        stage('Polaris') {
            /*when {
                anyOf {
                    environment name: 'FULLSCAN', value: 'true'
                    environment name: 'PRSCAN', value: 'true'
                }
            }*/
            steps {
                sh 'echo "We\'re now in the build phase, java home is " $JAVA_HOME'
                security_scan product: 'polaris',
                    polaris_application_name: "Evan Onboarding",
                    polaris_project_name: "Jenkins scan",
                    polaris_assessment_types: 'SAST,SCA',
                    polaris_branch_name: "Jenkins scan",
                    detect_search_depth: 20,
                    polaris_prComment_enabled: true,
                    polaris_reports_sarif_create: true,
                    //mark_build_status: 'UNSTABLE',
                    include_diagnostics: false
            }
        } 
    } 
    post {
        always {
            archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/bridge.log, .bridge/*/idir/build-log.txt'
            //zip archive: true, dir: '.bridge', zipFile: 'bridge-logs.zip'
            cleanWs()
        }
    }
}

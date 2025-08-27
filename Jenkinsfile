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
        JAVA_HOME = '/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    echo JAVA_HOME is $JAVA_HOME\nPATH is $PATH
                    
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
                sh 'echo heres what resides in the folder! '
                sh 'ls /var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/'
                sh 'ls /var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17'
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

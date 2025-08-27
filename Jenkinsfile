pipeline {
    agent any
    tools {
        maven 'maven-3.9.11'
        jdk 'openjdk-17'
    }
    stages {
        stage('Build and set up Java') {
            steps {
                sh '''
                    export JAVA_HOME=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17
                    export PATH=$JAVA_HOME/bin:$PATH
                    export network_ssl_trustAll=true

                    mvn spotless:apply && mvn -N wrapper:wrapper && chmod +x ./mvnw
                '''                
            }
        }
        stage('Coverity') {
            /*when {
                anyOf {
                    environment name: 'FULLSCAN', value: 'true'
                    environment name: 'PRSCAN', value: 'true'
                }
            }*/
            steps {
                security_scan product: 'coverity',
                    coverity_url: 'https://ec2-13-57-6-68.us-west-1.compute.amazonaws.com:8443',
                    coverity_project_name: "webgoat",
                    coverity_stream_name: "webgoat",
                    coverity_local: true,
                    coverity_policy_view: 'Outstanding Issues',
                    coverity_prComment_enabled: true,
                    //mark_build_status: 'UNSTABLE',
                    coverity_args: "-o commit.connect.description=$BUILD_TAG",
                    //network_ssl_trustAll: true,
                    include_diagnostics: false
            }
        }
    }
    post {
       always {
           archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/bridge.log, .bridge/*/idir/build-log.txt'
           // zip archive: true, dir: '.bridge', zipFile: 'bridge-logs.zip'
           cleanWs()
       }
    } 
}

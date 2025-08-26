pipeline {
    agent any
    tools {
        maven 'maven-3.9'
        jdk 'openjdk-17'
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    
                    export JAVA_HOME=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17
                    export PATH=$JAVA_HOME/bin:$PATH
                
                    echo "JAVA_HOME: $JAVA_HOME"
                    echo "PATH: $PATH"
                    which java
                    java -version

                    
                    mvn spotless:apply && mvn -N wrapper:wrapper && chmod +x ./mvnw
                    ./mvnw install -DskipTests
                '''                
                /*sh 'export PATH=$JAVA_HOME/bin:$PATH'
                sh 'java -version'
                sh 'mvn spotless:apply && mvn -N wrapper:wrapper && chmod +x ./mvnw'
                sh './mvnw install -DskipTests' */
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
                    coverity_project_name: "webgoat",
                    coverity_stream_name: "webgoat",
                    // Uncomment the coverity_local line below if using traditional Coverity deployments or 
                    // Cloud Native Coverity (CNC) with scan services disabled
                    coverity_local: true,
                    coverity_policy_view: 'Outstanding Issues',
                    coverity_prComment_enabled: true,
                    //mark_build_status: 'UNSTABLE',
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
        // Include Coverity stage detailed in scripted language instructions
        // in addition to environment and post sections   
}

pipeline {
    agent any

    tools {
        maven 'maven-4.0'
        jdk 'openjdk-17'
    }

    stages {
        stage('Build') {
            steps {
                withEnv([
                    'JAVA_HOME=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17',
                    'PATH=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17/bin:' + env.PATH
                ]) {
                    sh '''
                        ls /var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation
                        ls /var/lib/jenkins/tools/hudson.tasks.Maven_MavenInstallation/maven
                        mvn spotless:apply
                        mvn -N wrapper:wrapper
                        chmod +x ./mvnw
                    '''
                }
            }
        }

        stage('Polaris') {
            steps {
                withEnv([
                    'JAVA_HOME=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17',
                    'PATH=/var/lib/jenkins/tools/hudson.model.JDK/openjdk-17/jdk-17/bin:' + env.PATH
                ]) {
                    security_scan product: 'polaris',
                        polaris_application_name: "Evan Onboarding",
                        polaris_project_name: "Jenkins scan",
                        polaris_assessment_types: 'SAST,SCA',
                        polaris_branch_name: "Jenkins scan",
                        detect_search_depth: 20,
                        polaris_prComment_enabled: true,
                        polaris_reports_sarif_create: true,
                        include_diagnostics: false
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts allowEmptyArchive: true, artifacts: '.bridge/bridge.log, .bridge/*/idir/build-log.txt'
            cleanWs()
        }
    }
}

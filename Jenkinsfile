pipeline {
    agent any
    tools {
        maven "Maven3"
        jdk "Oraclejdk8"

    }
    environment{
        SNAP_REPO = 'vprofile-snapshot'
        NEXUS_USER = 'admin'
        NEXUS_PASS = '123456'
        RELEASE_REPO = 'vprofile-relese'
        CENTRAL_REPO = 'vpro-maven-central'
        NEXUSIP = '172.31.8.173'
        NEXUSPORT = '8081'
        NEXUS_GRP_REPO = 'vpro-maven-group'
        NEXUS_LOGIN = 'NexusLogin'
    }
    stages {
        stage('Build'){
          steps {
            sh 'mvn -s settings.xml -DskipTests install'

          }
    }
    }
}
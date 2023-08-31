pipeline {
    agent any
    tools {
        maven "Maven3"
        jdk "OracleJDK8"

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
        SONARSERVER = 'sonarserver'
        SONARSCANNER = 'sonarscaner'
    }
    stages {
        stage('Build'){
          steps {
            sh 'mvn -s settings.xml -DskipTests install'
            }
          post{
            success {
            echo "now Archiving"
            archiveArtifacts artifacts: '**/*.war'
          }
     
          }
        }
        stage('Test'){
            steps {
                sh 'mvn -s settings.xml test'
            }
        }
        stage ('Checkstyle Analysis') {
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
          
		     environment {
                scannerHome = tool "${SONARSCANNER}"
            }

             steps {
                withSonarQubeEnv("${SONARSERVER}") {
                sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                      -Dsonar.projectName=vprofile-repo \
                      -Dsonar.projectVersion=1.0 \
                      -Dsonar.sources=src/ \
                      -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                      -Dsonar.junit.reportsPath=target/surefire-reports/ \
                      -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                      -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

              
          }
        }
        stage("Quality Gate"){

             steps{  timeout(time: 10, unit: 'MINUTES') {
                  waitForQualityGate abortPipeline: true
            }
            
        }
              
        }

        stage("Nexus upload"){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [
                         artifactId: "vprofile",
                         classifier: '',
                         file: 'target/vprofile-v2.war',
                         type: 'war'   
                        ]
                    ]
                )
            }
        }

        }
    }

    
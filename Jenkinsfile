pipeline {

  agent any
  environment {
    DOCKER_CREDS = credentials('docker-credentials')
  }

  stages {

    stage('Build') {
      agent {
        docker {
          image 'maven:3.6.3-openjdk-11-slim'
        }
      }
      steps {
        sh 'mvn package'
      }
      // post {
      //   success {
      //     archiveArtifacts artifacts: 'target/*.jar', fingerprint: true, onlyIfSuccessful: true
      //     mail bcc: '', body: 'El pipeline fue exitoso', cc: '', from: '', replyTo: '', subject: 'Pipeline exitoso', to: 'josealdotrucios@gmail.com'
      //   }
      // }
    }
    // stage('SonarQube analysis') {
    //   steps {
    //     script {
    //       def scannerHome = tool 'scanner-default';
    //       withSonarQubeEnv('sonar-server') {
    //         sh "${scannerHome}/bin/sonar-scanner \
    //                         -Dsonar.projectKey=labmaven01 \
    //                         -Dsonar.projectName=labmaven01 \
    //                         -Dsonar.sources=src/main \
    //                         -Dsonar.sourceEncoding=UTF-8 \
    //                         -Dsonar.language=java \
    //                         -Dsonar.tests=src/test \
    //                         -Dsonar.junit.reportsPath=target/surefire-reports \
    //                         -Dsonar.surefire.reportsPath=target/surefire-reports \
    //                         -Dsonar.jacoco.reportPath=target/jacoco.exec \
    //                         -Dsonar.java.binaries=target/classes \
    //                         -Dsonar.java.coveragePlugin=jacoco \
    //                         -Dsonar.coverage.jacoco.xmlReportPaths=target/jacoco.xml \
    //                         -Dsonar.exclusions=**/*IT.java,**/*TEST.java,**/*Test.java,**/src/it**,**/src/test**,**/gradle/wrapper** \
    //                         -Dsonar.java.libraries=target/*.jar"
    //       }
    //     }
    //   }
    // }
  }
}
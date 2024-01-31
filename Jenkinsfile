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
      post {
        success {
          archiveArtifacts artifacts: 'target/*.jar', fingerprint: true, onlyIfSuccessful: true
        }
      }
    }
    stage('SonarQube analysis') {
      agent {
        docker {
          image 'maven:3.6.3-openjdk-11-slim'
        }
      }
      steps {

        git credentialsId: 'github',
          url: 'https://github.com/amefu/galaxy-jenkins-lab-alfredo-mendoza.git',
          branch: 'master'

        script {
          def scannerHome = tool 'scanner-default';;
          withSonarQubeEnv('sonar-server') {
            sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=labmaven01 \
                            -Dsonar.projectName=labmaven01 \
                            -Dsonar.sources=src/main \
                            -Dsonar.sourceEncoding=UTF-8 \
                            -Dsonar.language=java \
                            -Dsonar.tests=src/test \
                            -Dsonar.junit.reportsPath=target/surefire-reports \
                            -Dsonar.surefire.reportsPath=target/surefire-reports \
                            -Dsonar.jacoco.reportPath=target/jacoco.exec \
                            -Dsonar.java.binaries=target/classes \
                            -Dsonar.java.coveragePlugin=jacoco \
                            -Dsonar.coverage.jacoco.xmlReportPaths=target/jacoco.xml \
                            -Dsonar.exclusions=**/*IT.java,**/*TEST.java,**/*Test.java,**/src/it**,**/src/test**,**/gradle/wrapper** \
                            -Dsonar.java.libraries=target/*.jar"
          }
        }
      }
    }

    stage('Build Image') {
      steps {
        copyArtifacts filter: 'target/*.jar',
          fingerprintArtifacts: true,
          projectName: '${JOB_NAME}',
          flatten: true,
          selector: specific('${BUILD_NUMBER}'),
          target: 'build/libs/'
        sh 'docker --version'
        sh 'docker-compose --version'
        sh 'docker-compose build'
      }
    }
    stage('Publish Image') {
      steps {
        script {
          sh 'docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}'
          sh 'docker tag msmicroservice ${DOCKER_CREDS_USR}/msmicroservice:$BUILD_NUMBER'
          sh 'docker push ${DOCKER_CREDS_USR}/msmicroservice:$BUILD_NUMBER'
          sh 'docker logout'
        }
      }
    }
    stage('Run Container') {
      steps {
        script {
          sh 'docker login -u ${DOCKER_CREDS_USR} -p ${DOCKER_CREDS_PSW}'
          sh 'docker rm galaxyLab -f'
          sh 'docker run -d -p 8080:8080 --name galaxyLab ${DOCKER_CREDS_USR}/msmicroservice:$BUILD_NUMBER'
          //sh 'docker run -d -p 8080:8080 ${DOCKER_CREDS_USR}/msmicroservice:$BUILD_NUMBER'
          sh 'docker logout'
        }
      }
    }
    stage('Test Run Container') {
      steps {
        script {
          sh 'docker ps'
          //    sh 'curl http://192.168.1.17:8080/customers'
        }
      }
    }

    stage('Crear Folder y Pipeline') {
      steps {
        script {
          // Crear el Folder galaxy-examen usando sh mkdir
          def folderName = 'galaxy-examen'
          sh "mkdir -p ${folderName}"

          // Crear el archivo Jenkinsfile (pipeline-final) dentro de galaxy-examen
          def pipelineScript = ""
          "
          pipeline {
            agent any
            stages {
              stage('Llamar Repositorio') {
                steps {
                  script {
                    // llama al repositorio donde se encuentra el jenkinsfile actual
                    git credentialsId: 'github',
                      url: 'https://github.com/amefu/galaxy-jenkins-lab-alfredo-mendoza.git',
                      branch: 'main'
                  }
                }
              }
            }
          }
          ""
          "
          writeFile(file: "${folderName}/pipeline-final", text: pipelineScript.trim())
        }
      }
    }
  }
}
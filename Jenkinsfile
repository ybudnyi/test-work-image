pipeline {
  agent any
  environment {
    VERSION = '1.0'
    BRANCH = 'main'
    PRODUCT_REPO = "https://github.com/ybudnyi/test-work-image.git"
    VCS_TOKEN = credentials('github')
    DOCKER = credentials('docker')
    SONAR=credentials('sonar')

  }
options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '3'))
}

      stages {
        stage('PULLfromVCS') {
            steps {
                git (branch: "${BRANCH}", credentialsId: "github", url: "${PRODUCT_REPO}")
            }
        }
        stage('RUN_TESTS') {
            steps {
                sh 'echo "test"'
            }
        }
        // stage('CODE INSPECTION WITH SONARQUBE') {
        //     steps {
        //             sh "/Users/yuriibudnyi/.sonar/sonar-scanner-4.7.0.2747-macosx/bin/sonar-scanner \
        //                 -Dsonar.organization=test-work \
        //                 -Dsonar.projectKey=test-work \
        //                 -Dsonar.sources=./docker \
        //                 -Dsonar.host.url=https://sonarcloud.io \
        //                 -Dsonar.login=${SONAR}"
        //     }
        // }
        stage('BUILD_IMAGES') {
            steps {
                sh "cd docker && /usr/local/bin/docker build -t ybudnyi/test-work:${VERSION}.${env.BUILD_ID} ."
            }
        }
        stage('PUSH IMAGES') {
            steps {
                sh "/usr/local/bin/docker login -u ybudnyi -p ${DOCKER}"
                sh "/usr/local/bin/docker push ybudnyi/test-work:${VERSION}.${env.BUILD_ID}"
            }
        }
    }
    post {
        always{
            cleanWs()
        success{
                    build job: 'change-charts', parameters: [string(name: 'TAG', value: "${env.BUILD_ID}")] , propagate: false
                }
        }
    }
}
pipeline {
  agent any
  environment {
    VERSION = '1.0'
    BRANCH = 'main'
    PRODUCT_REPO = "https://github.com/ybudnyi/test-work-image.git"
    VCS_TOKEN = credentials('github')
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
        stage('CODE INSPECTION WITH SONARQUBE') {
            steps {
                    sh "export SONAR_TOKEN=${SONAR}"
                    sh '/Users/yuriibudnyi/.sonar/sonar-scanner-4.7.0.2747-macosx/bin/sonar-scanner \
                        -Dsonar.organization=test-work \
                        -Dsonar.projectKey=test-work \
                        -Dsonar.sources=./docker/ \
                        -Dsonar.host.url=https://sonarcloud.io'
            }
        }
        stage('BUILD_IMAGES') {
            steps {
                sh "cd docker && /usr/local/bin/docker build -t ghcr.io/ybudnyi/test-work-image:${VERSION}.${env.BUILD_ID} ."
            }
        }
        stage('PUSH IMAGES') {
            steps {
                sh "echo ${VCS_TOKEN} | docker login ghcr.io -u ybudnyi --password-stdin"
                sh "docker push ghcr.io/ybudnyi/test-work-image:${VERSION}.${env.BUILD_ID}"
            }
        }
    }
    post {
        always{
            cleanWs()
        // success{
        //             build job: 'Dashvis_CD', parameters: [string(name: 'TAG', value: "${env.BUILD_ID}")] , propagate: false
        //         }
        }
    }
}
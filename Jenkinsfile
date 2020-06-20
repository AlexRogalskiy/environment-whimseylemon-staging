pipeline {
  agent {
    label "jenkins-go"
  }
  environment {
    ORG = 'collabnix'
    APP_NAME = 'environment-whimseylemon-staging'
    CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
    DOCKER_REGISTRY_ORG = 'famous-hull-276807'
  }
  stages {
    stage('CI Build and push snapshot') {
      when {
        branch 'PR-*'
      }
      environment {
        PREVIEW_VERSION = "0.0.0-SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER"
        PREVIEW_NAMESPACE = "$APP_NAME-$BRANCH_NAME".toLowerCase()
        HELM_RELEASE = "$PREVIEW_NAMESPACE".toLowerCase()
      }
      steps {
        container('gcr.io/jenkinsxio/builder-go') {
          dir('env') {
            sh "jx step helm build"
          }
        }
      }
    }
    stage('Build Release') {
      when {
        branch 'master'
      }
      steps {
        container('gcr.io/jenkinsxio/builder-go') {

          // ensure we're not on a detached head
          sh "git checkout master"
          sh "git config --global credential.helper store"
          sh "jx step git credentials"
          dir('env') {
            sh "jx step helm apply"
          }
        }
      }
    }
  }
  post {
        always {
          cleanWs()
        }
  }
}

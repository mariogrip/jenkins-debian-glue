pipeline {
  agent any
  stages {
    stage('Preparation') {
      steps {
        dir(path: 'source') {
          git 'https://github.com/mika/jenkins-debian-glue.git'
        }
        
      }
    }
    stage('Build source') {
      steps {
        sh 'rm -f ./* || true'
      }
    }
    stage('Build binary - armhf') {
      steps {
        sh '''export architecture="armhf"
export REPOS="xenial"
#/usr/bin/generate-reprepro-codename "${REPOS}"
#/usr/bin/build-and-provide-package'''
      }
    }
    stage('Results') {
      steps {
        archiveArtifacts '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo'
      }
    }
    stage('Cleanup') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)
      }
    }
  }
}
pipeline {
  agent any
  def gitCommit
  def gitBranch
  stages {
    stage('Preparation') {
      steps {
        dir(path: 'source') {
          git 'https://github.com/mariogrip/jenkins-debian-glue.git'
          gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
          gitBranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
        } 
      }
    }
    stage('Build source') {
      steps {
        sh 'rm -f ./* || true'
        withEnv(["GIT_COMMIT=${gitCommit}", "GIT_BRANCH=${gitBranch}"]) {
            sh '/usr/bin/generate-git-snapshot'
        }
      }
    }
    stage('Build binary - armhf') {
      steps {
        sh '''export architecture="armhf"
export REPOS="xenial"
/usr/bin/generate-reprepro-codename "${REPOS}"
/usr/bin/build-and-provide-package'''
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

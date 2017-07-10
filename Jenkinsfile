pipeline {
  agent any
  stages {
    stage('Preparation') {
      steps {
        dir(path: 'source') {
          git 'https://github.com/mariogrip/jenkins-debian-glue.git'
          script {
            def gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
            def gitBranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
          }
          
        }
        
      }
    }
    stage('Build source') {
      steps {
        sh 'rm -f ./* || true'
        sh '''cd source
export GIT_COMMIT=$(git rev-parse HEAD)
export GIT_BRANCH=$(git rev-parse --abbrev-ref HEAD)
cd ..
/usr/bin/generate-git-snapshot
'''
        stash(name: 'source', includes: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo,lintian.txt')
      }
    }
    stage('Build binary - armhf') {
      steps {
        node(label: 'xenial-armhf') {
          unstash 'source'
          sh '''export architecture="armhf"
export REPOS="xenial"
export BUILD_ONLY=true
/usr/bin/generate-reprepro-codename "${REPOS}"
/usr/bin/build-and-provide-package'''
          stash(includes: '*.deb,*.changes,*.buildinfo,lintian.txt', name: 'build')
        }
        
      }
    }
    stage('Results') {
      steps {
        unstash 'build'
        archiveArtifacts(artifacts: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo', fingerprint: true, onlyIfSuccessful: true)
        sh '''export architecture="armhf"
export REPOS="xenial"
export PROVIDE_ONLY=true
/usr/bin/build-and-provide-package'''
      }
    }
    stage('Cleanup') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)
      }
    }
  }
}
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
        sh '/usr/bin/build-source.sh'
        stash(name: 'source', includes: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo,lintian.txt')
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
      }
    }
    stage('Build binary - armhf') {
      steps {
        node(label: 'xenial-arm64') {
          unstash 'source'
          sh '''export architecture="armhf"
export BUILD_ONLY=true
/usr/bin/build-and-provide-package'''
          stash(includes: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo,lintian.txt', name: 'build')
          cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
        }
        
      }
    }
    stage('Results') {
      steps {
        unstash 'build'
        archiveArtifacts(artifacts: '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo', fingerprint: true, onlyIfSuccessful: true)
        sh '''export architecture="armhf"
/usr/bin/build-repo.sh'''
      }
    }
    stage('Cleanup') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
      }
    }
  }
}
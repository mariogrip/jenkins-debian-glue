pipeline {
  agent any
    def gitCommit
    def gitBranch
     stage('Preparation') {
        dir('source') {
            git 'https://github.com/mika/jenkins-debian-glue.git'
            gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
            gitBranch = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
        }
  }
    stage('Build source') {
        
        sh 'rm -f ./* || true'
        withEnv(["GIT_COMMIT=${gitCommit}", "GIT_BRANCH=${gitBranch}"]) {
            sh '/usr/bin/generate-git-snapshot'
        }
   }
    stage('Build binary') {
        sh '''export architecture="armhf"
        export REPOS="xenial"
        /usr/bin/generate-reprepro-codename "${REPOS}"
        /usr/bin/build-and-provide-package'''
   }
    stage('Results') {
         archive '*.gz,*.bz2,*.xz,*.deb,*.dsc,*.changes,*.buildinfo'
   }
    stage("Clean") {
        cleanWs()
   }
}

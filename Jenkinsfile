node {
  stage('SCM') {
    checkout scm
  }
  stage('SonarQube Analysis') {
    def scannerHome = tool 'SonarScanner';
    withSonarQubeEnv(SonarScanner) {
      sh "${scannerHome}/bin/sonar-scanner"
    }
  }
}

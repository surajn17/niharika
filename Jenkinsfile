pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                // Checkout code from Git
                checkout scm
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Use SonarScanner tool
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube Scanner') {
                        // Run SonarQube analysis
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM',
                        branches: [[name: '**']], // Fetch all branches
                        userRemoteConfigs: [[url: 'https://github.com/surajn17/niharika.git']],
                        extensions: [[$class: 'WildcardSCMHeadFilter', includes: ['*']]] // Include all branches
                    ])
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('SonarScanner') {
                        def scannerOutput = sh(script: "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=my-sonar -Dsonar.sources=. -Dsonar.host.url=http://54.88.184.130:9000 -Dsonar.login=sqp_52f9b314607b0f7745878c4c3af0f58451c74aaf", returnStatus: true)
                        if (scannerOutput == 0) {
                            echo "SonarQube analysis passed!"
                        } else {
                            error "SonarQube analysis failed. Quality Gate did not pass!"
                        }
                    }
                }
            }
        }

        stage('Package and Deploy') {
            steps {
                script {
                    def deployServer = 'ubuntu@54.146.57.215' // Replace with your deployment server
                    sh "zip -r code.zip ."
                    sh "scp -o StrictHostKeyChecking=no code.zip ${deployServer}:/var/www/html/niharika"

                    // SSH into the server and perform required commands
                    sh "ssh -o StrictHostKeyChecking=no ${deployServer} 'unzip -o /var/www/html/niharika/code.zip -d /var/www/html/niharika && rm /var/www/html/niharika/code.zip'"
                }
            }
        }

        stage('Send SNS Notification') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'AWS_ACCESS_KEY_ID', variable: 'AWS_ACCESS_KEY_ID'),
                                     string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh """
                        aws sns publish --topic-arn arn:aws:sns:us-east-1:352403467408:niharika --message "Jenkins Pipeline succeeded!" --region us-east-1
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            emailext attachLog: true,
                body: 'Jenkins Pipeline execution completed!',
                subject: "${currentBuild.fullDisplayName} ${currentBuild.result}",
                to: 'niharika.tripathi@kadellabs.com'
        }
    }
}

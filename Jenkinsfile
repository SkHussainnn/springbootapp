pipeline {
    agent {
        node {
            label 'Jenkins-Slave-Node'
        }
    }
    //env setup
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }
    stages {
        stage("Build Code") {
            steps {
                echo "Build started"
                script {
                    sh 'mvn clean deploy -Dmaven.test.skip=true'
                }
                echo "Build completed" 
            }
        }
        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'Sonarqube-scanner-meportal'
            }
            steps{
                withSonarQubeEnv('Sonar-server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

    }
}



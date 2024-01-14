pipeline {
    agent {
        node {
            label 'Jenkins-Slave-Node'
        }
    }
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
                echo "Build completed successfully"
            }
        }
    }
}

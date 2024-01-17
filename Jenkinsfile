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
        /*stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'Sonar-Scanner-meportal'
            }
            steps{
                withSonarQubeEnv('Sonar-server-meportal') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                } //AVD GRP
            }
        }*/
        stage("Artifact Publish") {
            steps {
                script {
                    echo '------------- Artifact Publish Started ------------'
                    def server = Artifactory.newServer url:"https://hussains.jfrog.io//artifactory" ,  credentialsId:"Jfrog-credential"
                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "staging/(*)",
                                "target": "release-local-artifacts/{1}",
                                "flat": "false",
                                "props" : "${properties}",
                                "exclusions": [ "*.sha1", "*.md5"]
                            }
                        ]
                    }"""
                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                    echo '------------ Artifact Publish Ended -----------'  
                }
            }   
        }
        stage(" Create Docker Image ") {
            steps {
                script {
                    echo '-------------- Docker Build Started -------------'
                    app = docker.build("hussains.jfrog.io/mymeportal-docker-local/myapp:1.0.1")
                    echo '-------------- Docker Build Ended -------------'
                }
            }
        }

        stage (" Docker Publish "){
            steps {
                script {
                        echo '---------- Docker Publish Started --------'  
                        docker.withRegistry("https://hussains.jfrog.io", 'Jfrog-credential'){
                        app.push()
                        echo '------------ Docker Publish Ended ---------'  
                    }    
                }
            }
        }
        /*stage ("Deploy Stage"){
            steps {
                script {
                        sh './deploy.sh'   //Hussainhbjhbjhb
                    }    
                }
            }
            
        }*/
        stage("Deploy") {
    steps {
        script {
            echo '<--------------- Helm Deploy Started --------------->'
            sh 'helm install meportal /home/ubuntu/meportal-1.0.1.tgz'
            echo '<--------------- Helm deploy Ends --------------->'
        }
    }
}
    }
}
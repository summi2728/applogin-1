pipeline {
   /* agent {
        docker { image 'image_name' }
    } */
    agent {label "ec2-fleet"}
    stages {
        stage ("git clone") {
            steps {
                git url: 'https://github.com/tejesh555/applogin.git'
                git url: ' https://github.com/tejesh555/ansible2.git'
            }
        }
        stage ("build") {
            steps {
                sh "mvn clean install"
            }
        }
        stage ("test") {
            steps {
                script {
                    def scannerHome = tool 'mysonarscanner';
                            withSonarQubeEnv('mysonar') {
                            sh "${scannerHome}/bin/sonar-scanner \
                            -D sonar.login=admin \
                            -D sonar.password=Nov@2022 \
                            -D sonar.projectKey=applogin \
                            -D sonar.exclusions=vendor/**,resources/**,**/*.java \
                            -D sonar.host.url=http://13.126.34.226:9000/"
                        }
                }
            }
        }
        stage ("publish") {
            steps {
                script {
                    rtUpload (
                        serverId: 'myjfrog',
                        spec: '''
                            {
                                "files": [
                                    {
                                        "pattern": "target/*.war",
                                        "target": "applogin"
                                    }
                                ]
                            }
                        ''',
                        buildName: "${JOB_NAME}",
                        buildNumber: "${BUILD_NUMBER}"
                    )
                }
            }
        }
    }
}


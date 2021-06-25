pipeline {
    agent any

    options {
        // This is required if you want to clean before build with the "Workspace Cleanup Plugin"
        skipDefaultCheckout(true)
    }

    environment {
        SONARQUBE_URL = '<SonarQube URL>' // Replace with your SonarQube server URL
    }    

    stages {
        stage('SCM') {
            steps {
                // Clean before build using the "Workspace Cleanup Plugin"
                cleanWs()
                checkout scm
            }
        }

        stage('Download Build Wrapper') {
            steps {
                sh '''
                  mkdir -p .sonar
                  curl -sSLo .sonar/build-wrapper-linux-x86.zip ${SONARQUBE_URL}/static/cpp/build-wrapper-linux-x86.zip 
                  unzip -o .sonar/build-wrapper-linux-x86.zip -d .sonar/
                '''
            }
        }

        stage('Build') {
            steps {
                sh ''' 
                  mkdir build                 
                  cmake -S . -B build
                  .sonar/build-wrapper-linux-x86/build-wrapper-linux-x86-64 --out-dir bw-output cmake --build build/ --config Release
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'; // Name of the SonarQube Scanner you created in "Global Tool Configuration" section
                    withSonarQubeEnv() {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
    }
}

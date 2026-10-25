pipeline {
    agent any
    
    environment{
         SONAR_HOME = tool "sonar"
    }
    stages {
               stage("Workspace cleanup"){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        
        stage('Git: Code Checkout') {
            steps {
                    sh "git clone https://github.com/pooja-ja/Jenkins-CI-CD.git"
            }
        }
        
        stage("Trivy: Filesystem scan"){
            steps{
                script{
                    sh "trivy fs ."
                }
            }
        }
        stage("Dependency scan"){
             steps{
                      sh "pip3 install pip-audit --break-system-packages" 
                      sh "~/.local/bin/pip-audit -r Jenkins-CI-CD/requirements.txt --no-deps"
             }
        }   
        stage("SonarQube: Code Analysis"){
             steps{
                     withSonarQubeEnv("sonar"){
                      sh "$SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=nodejs -Dsonar.projectKey=nodejs -X"
                     }
             }
        }
        stage("SonarQube: Code Quality Gates"){
            steps{
                  timeout(time: 1, unit: "MINUTES"){
                  waitForQualityGate abortPipeline: true
                 }
            }
        } 
      stage("Code Test"){
              steps{
                       sh "pip3 install --break-system-packages -r Jenkins-CI-CD/requirements.txt && python3 -m pytest Jenkins-CI-CD/test_app.py -v"
              }
         }        
          stage("deploy"){
              steps{
                  dir('Jenkins-CI-CD/') {
                     sh "docker-compose up -d"
             }
             }
        } 
}
}
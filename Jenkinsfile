pipeline {
    agent any
    tools {
        maven "Maven"   
    }    
    stages {
        stage('Compile-Build-Test ') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('sonarqube'){
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage("Quality Gate"){
            steps{
                echo "This is quality gate analysis"
                script{
                timeout(time: 1, unit: 'MINUTES') { // Just in case something goes wrong, pipeline will be killed after a timeout
                    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
                }
            }
           
        }
        stage('Pushing to Nexus'){
            steps{
             nexusArtifactUploader artifacts: [[artifactId: 'BMI', classifier: 'BMI Calculator Application', file: 'pom.xml', type: 'war']], credentialsId: 'nexus-credentialss', groupId: 'comrades.bmi', nexusUrl: '18.224.155.110:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'devopstraining', version: 'BMI-4.0'
           
            }
        }
        stage('Deployment to AWS'){
            steps{
            deploy adapters: [tomcat9(credentialsId: 'tomcatCredentials', path: '', url: 'http://3.15.0.139:8088/')], contextPath: 'BMI', onFailure: false, war: '**/target/*.war'
            }
        }
      }
    post{
        always{
            echo "This is always run"
        }
        success{
            echo "This is run only if successful"
        }
        failure{
            echo "This is failure part"
        }
        unstable{
            echo "This is unstable part"
        }
        changed{
            echo "It is run only on any changes made to jenkins file"
        }
        
        
    }
}

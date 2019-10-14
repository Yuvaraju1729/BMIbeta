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
        stage('Pushing to Nexus'){
            steps{
                <!-- nexusArtifactUploader artifacts: [[artifactId: 'BMI', classifier: 'BMI Calculator Application', file: 'pom.xml', type: 'war']], credentialsId: 'nexus-credentialss', groupId: 'comrades.bmi', nexusUrl: '18.224.155.110:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'devopstraining', version: 'BMI-4.0' -->
                curl --upload-file **/target/*.war -u trainee:trainee -v http://18.224.155.110:8081/nexus/#view-repositories;devopstraining~browsestorage
            
            }
        }
        stage('Deployment to AWS'){
            steps{
            deploy adapters: [tomcat9(credentialsId: 'tomcatCredentials', path: '', url: 'http://3.15.0.139:8088/')], contextPath: 'BMI', onFailure: false, war: '**/target/*.war'
            }
        }
      }
}

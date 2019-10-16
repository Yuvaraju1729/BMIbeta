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
                     sh 'mvn clean package sonar:sonar'
                }
            }
        }
        
        stage('Pushing to Nexus'){
            steps{
             nexusArtifactUploader artifacts: [[artifactId: 'BMI', classifier: 'BMI Calculator Application', file: 'pom.xml', type: 'war']], credentialsId: 'nexus-credentialss', groupId: 'comrades.bmi', nexusUrl: '18.224.155.110:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'devopstraining', version: 'BMI-$(BUILD_NUMBER)'
           
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

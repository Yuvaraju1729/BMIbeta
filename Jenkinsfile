pipeline {
    agent any
    tools {
        maven "Maven"   
    }   
    environment{
        sonarscanner = tool 'SonarScanner'
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
                     sh '${sonarscanner}/bin/sonar-scanner -Dproject.settings=./sonar-project.properties'
                }
            }
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 3, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
        stage('Pushing to Nexus'){
            steps{
               withCredentials([usernamePassword(credentialsId: 'nexus-credentialss', passwordVariable: 'password', usernameVariable: 'username'),string(credentialsId: 'NEXUS_URL', variable: 'nexus_url')]){
                    //sh 'curl -v -F r=devopstraining -F hasPom=true -F e=jar -F file=@pom.xml -F file=@target/BMIBeta-${BUILD_NUMBER}.jar -u ${username}:${password} http://18.224.155.110:8081/nexus/content/repositories/devopstraining/comrades/Calc-${BUILD_NUMBER}.jar'
                    //sh 'curl -u ${username}:${password} --upload-file target/BMIBeta-${BUILD_NUMBER}.jar http://18.224.155.110:8081/nexus/content/repositories/devopstraining/comrades/Calc.jar'
                    sh 'curl -F file=@target/BMI-0.war -u ${username}:${password} http://${nexus_url}/nexus/content/repositories/devopstraining/comrades/BMI-${BUILD_NUMBER}.war'
               }
            }
        }
        stage('Uploading artifact to Ansible Server'){
            steps{
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '**/target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }   
        stage('Uploading artifact to all hosts from ansible'){
            steps{
                sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/project-ansible.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        } 
        /*stage('Deployment to AWS'){
            steps{
            withCredentials([usernamePassword(credentialsId: 'tomcatCredentials', passwordVariable: 'password', usernameVariable: 'username'),string(credentialsId: 'TOMCAT_URL', variable: 'tomcat_url')]){
                    sh 'curl ${tomcat_url}/manager/text/undeploy?path=/CALC -u ${username}:${password}'
                    sh 'curl -v -u ${username}:${password} -T target/BMI${BUILD_NUMBER}.war ${tomcat_url}/manager/text/deploy?path=/CALC'
                }
            }
        }*/
      }
}
}

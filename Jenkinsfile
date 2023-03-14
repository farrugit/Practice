pipeline{
    
    agent any 
    tools{
        maven 'maven3.3.9'
        jdk 'jdk8'
    }
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/farrugit/Practice.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
                      
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
               }
                    
            }
        }
          /*stage('Quality Gate Status'){
                
                steps{
                    
                    script{

                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                    }
                }
            }*/
        stage('Upload jar file to Nexus'){
            steps{
                script{
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    def nexusRepo = readPomVersion.version.endswith("SNAPSHOT") ? "jenkins-maven-snapshot" : "jenkins_maven"
                    nexusArtifactUploader artifacts: [[artifactId: 'springboot', 
                                                       classifier: '', 
                                                       file: 'target/Uber.jar', 
                                                       type: 'jar']],
                        credentialsId: 'nexus', 
                        groupId: 'com.example', 
                        nexusUrl: 'nexus:8081', 
                        nexusVersion: 'nexus3',
                        protocol: 'http', 
                        repository: 'jenkins_maven', 
                        version: "${readPomVersion.version}"
                }
            }
        }
     }      
}

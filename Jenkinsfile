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
                        
                        sh 'mvn clean package -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true sonar:sonar'
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
                    /*def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "jenkins-maven-snapshot" : "jenkins_maven"*/
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
        stage('docker image build'){
            steps{
                script{
                    sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh 'docker image tag $JOB_NAME:v1.$BUILD_ID sk0808/$JOB_NAME:v1.$BUILD_ID'
            
                }
            }
        }
        stage('Image push to DockerHub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pwd', variable: 'docker_cred')])
                    {
                    sh 'docker login -u sk0808 -p ${docker_cred}'   
                    sh 'docker image push sk0808/$JOB_NAME:v1.$BUILD_ID'
                    }
                }
            }
        }
        stage('container deployment'){
        steps{
            script{
               /* sh 'docker login -u sk0808 -p ${docker_cred}'*/
                sh 'docker run --name demo -p 9090:9090 sk0808/$JOB_NAME:v1.$BUILD_ID'
            }
        }
        }
     }      
}

pipeline{
    
    agent any 
    
    tools { 
        maven 'maven 3' 
        jdk 'JDK 17' 
    }
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/Vishal2196-dt/demo-counter-app.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    bat 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    bat 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    bat 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-api') {
                        
                        bat 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
                    }
                }
            }
            stage('upload war file to nexus'){

                steps{

                    script{
                        def readPomVersion = readMavenPom file: 'pom.xml'
                        def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "javaapp-snapshot" :"javaapp-realese"
                        nexusArtifactUploader artifacts: 
                            [
                                [
                                    artifactId: 'springboot', 
                                    classifier: '', file: 'target/Uber.jar', 
                                    type: 'jar'
                                ]
                            ], 
                               credentialsId: 'nexus-auth',
                               groupId: 'com.example', 
                               nexusUrl: 'localhost:8081', 
                               nexusVersion: 'nexus3', 
                               protocol: 'http', 
                               repository: 'nexusRepo', 
                               version: "${readPomVersion.version}"
                    }
                }
            }
                    
        }
        
}

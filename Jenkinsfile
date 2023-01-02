pipeline {
    agent any 
    stages{
        stage('SCM Checkout'){
            steps{
                git branch: 'main', url: 'https://github.com/farouq08/demo-counter-app.git'
            }
        }
        stage('Unit test'){
            steps{
                sh 'mvn test'
            }
        }
        stage('Integration testing'){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Build'){
            steps{
            //clean war file   
                sh 'mvn clean install'
            }
        }
        stage('static code analysis'){
            steps{
                withSonarQubeEnv(installationName: 'sq1',credentialsId: 'sonar-api') {
                 sh 'mvn clean package sonar:sonar'
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
        stage('upload jar artifact to nexus repo'){
            steps{
                script{
                    //From jenkins documentation
                    def readPomVersion = readMavenPom file: 'pom.xml'
                    nexusArtifactUploader artifacts: 
                    [
                        [
                            artifactId: 'springboot', 
                            classifier: '', 
                            file: '/target/Uber.jar', type: 'jar'
                        ]
                    ], 
                    credentialsId: 'nexus-auth', 
                    groupId: 'com.example', 
                    nexusUrl: '172.17.0.2:8081', 
                    nexusVersion: 'nexus3', 
                    protocol: 'http', 
                    repository: 'http://localhost:8081/repository/maven-project/', 
                    version: '${readPomVersion}'
                }
            }
        }
    }
}

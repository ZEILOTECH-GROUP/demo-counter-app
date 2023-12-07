pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git branch: 'main', url: 'https://github.com/ZEILOTECH-GROUP/demo-counter-app.git'
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
                    
                    withSonarQubeEnv(credentialsId: 'sonarqube-api') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
                    
                }
            }
            stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube-api'
                    }
                }
            }
        stage('Docker Image Build'){
            steps{
                 sh """
                 docker image build -t rameshkumarverma/javaapp:latest . 
                 docker images
                 """
            }
        }
        stage('Docker Image Scan: trivy '){
            steps{
                sh """   
                 trivy image rameshkumarverma/javaapp:latest > scan.txt
                 cat scan.txt
                 """
            }
        }
        stage('Docker Image Push : DockerHub '){
            steps{
                 withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'pass', usernameVariable: 'user')]) {
                     sh """
                     docker login -u '${user}' -p '${pass}'
                     docker image push rameshkumarverma/javaapp:latest
                     """
                 }
            }
        }
        stage('Docker Image Cleanup : DockerHub '){
            steps{
                sh """
                docker rmi rameshkumarverma/javaapp:latest
                // docker rmi ${hubUser}/${project} ${hubUser}/${project}:latest
                """
            }
        }
    }
}

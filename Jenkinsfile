pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('GIT checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sagarkulkarni1989/Boardgame.git'
            }
        }
        
        // stage('Compile') {
        //     steps {
        //         sh 'mvn compile'
        //     }
        // }
        
        // stage('test') {
        //     steps {
        //         sh 'mvn test'
        //     }
        // }
        
        // stage('Trivy scan report') {
        //     steps {
        //         sh 'trivy fs --format table -o trivy-fs-report.html .'
        //     }
        // }
        
        // stage('Sonarqube scan analysis') {
        //     steps {
        //         withSonarQubeEnv('sonar') {
        //             sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame -Dsonar.java.binaries=. '''
        //         }
        //     }
        // }
        
        // stage('Quality Gate') {
        //     steps {
        //         waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
        //     }
        // }
        
        stage('MVN build') {
            steps {
                sh 'mvn package'
            }
        }
        
         stage('Publish to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy'
                }
            }
        }
        
        stage('Build Docker image') {
            steps {
                script{
                    
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t gita/boardshack:latest ."
                        
   
                    }
                }
            }
        }
        stage('DOcker Image Scan') {
            steps {
                sh 'trivy image --format table -o trivy-fs-report.html gita/boardshack:latest'
            }
        }
        
        stage('push Docker image') {
            steps {
                script{
                    
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push gita/boardshack:latest"
                        
   
                    }
                }
            }
        }
        
        stage('deployment on k8s') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8s-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://15.207.113.247:6443') {
                  
                   sh "kubectl apply -f deployment-service.yaml"
                }
            }
        }
        
        
         
    }
}

pipeline {
    agent any
    
    environment {
        SONAR_SCANNER_HOME = tool 'SonarQube Scanner'
        MAVEN_HOME = tool 'maven'
    }

    stages {
        stage('Checkout') {
            steps {
                // Reemplaza 'your_ssh_private_key' con el ID de la credencial de SSH que tienes configurada en Jenkins para acceder al repositorio
                git credentialsId: 'your_ssh_private_key', url: 'git@github.com:HugoMartinThielemannMoreno/DevopsTestMerge.git'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "${env.SONAR_SCANNER_HOME}/bin/sonar-scanner -X -Dsonar.projectKey=sonarqube -Dsonar.sources=src/main/java -Dsonar.java.binaries=target/classes"
                }
            }
        }
        
        stage('Maven Build') {
            steps {
                sh "${env.MAVEN_HOME}/bin/mvn clean install"
            }
        }
        
        stage('Test and Publish') {
            steps {
                sh "${env.MAVEN_HOME}/bin/mvn test"
                junit 'target/surefire-reports/*.xml'
                jacoco(execPattern: 'target/jacoco.exec')
            }
        }
        
        stage('Docker Build and Publish') {
            steps {
                sh 'docker login -u your_dockerhub_username -p your_dockerhub_password'
                sh 'docker build -t bellyster/devops-integracion:0.0.1 .'
                sh 'docker push bellyster/devops-integracion:0.0.1'
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                // Your deployment steps here
            }
        }
        
        stage('Production Deployment') {
            steps {
                sh 'ngrok http 8080 &'
                // Your ngrok configuration and webhook setup
            }
        }
    }
}

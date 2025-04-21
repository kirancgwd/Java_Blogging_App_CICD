pipeline { 
    agent any
     environment{
        SCANNER_HOME= tool 'sonar-scanner'
        IMAGE_TAG="v${BUILD_NUMBER}"
        }
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    
    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/kirancgwd/Java_Blogging-App.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        
        stage('Sonar Analysis') {
            steps {
                     withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=java-blogging-app  \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=java-blogging-app  '''
               }
            }
                
        }
        stage('File System Scan') {
            steps {
               sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
         stage('Build Jar') {
            steps {
                sh "mvn package"
            }
        }
         stage('Deploy to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'Global-maven', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                sh "mvn deploy -Dskiptests=true" //For dev env only to skip test
                    
                }
            }
        }
          stage('Docker Build') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker') {
                    sh "docker build -t  kiranpkdocker/java-blogging-app:$IMAGE_TAG . "
                 }
                 }
               }
             }
         stage('Docker Image Scan') {
            steps {
               sh "trivy image --format table -o image-report.html kiranpkdocker/java-blogging-app:$IMAGE_TAG "
            }
        }
         stage('Docker tag & push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'docker') {
                    sh "docker tag kiranpkdocker/java-blogging-app:$IMAGE_TAG kiranpkdocker/java-blogging-app:$IMAGE_TAG "
                    sh "docker push  kiranpkdocker/java-blogging-app:$IMAGE_TAG "
                    sh "docker run -d -p  8082:8082 kiranpkdocker/java-blogging-app:$IMAGE_TAG"
                 }
                 }
               }
             }
    }
}

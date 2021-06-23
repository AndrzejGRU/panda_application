pipeline {
    agent 
    {label 'Slave'}

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "m3"
    }
    environment {
        IMAGE = readMavenPom().getArtifactId()
        VERSION = readMavenPom().getVersion()
}
    stages {
        stage('Clear running apps') {
            steps {
                // clear running dockers from last session
                sh 'docker rm -f pandaapp || true'
                

            }
            }
        stage('Get code') {
            steps {
                // Get some code from a GitHub repository
                checkout scm
                

            }
            }
        stage('Build and Junit') {
            steps {
                sh 'mvn clean install'

            }
            }
        stage('Build Docker Image') {
            steps {
                sh "mvn package -Pdocker -Dmaven.test.skip=true"

            }
            }
        stage('Run Docker App based on image') {
            steps {
                sh "docker run -d -p 0.0.0.0:8080:8080 --name pandaapp -t ${IMAGE}:${VERSION}"

            }
            }
        stage('test Selenium') {
            steps {
                sh 'mvn test -Pselenium'

            }
        }
        stage('Deploy to arti') {
            steps {
                configFileProvider([configFile(fileId: 'c935b131-4ff0-4597-8070-890126ba51c4', variable: 'mavensettings')]) {
                sh 'mvn -s $mavensettings deploy -Dmaven.test.skip=true -e' 
            }
            
        }   
            }
        post {
         always { 
            sh 'docker stop pandaapp'
            deleteDir()
            }
    
        }
} 
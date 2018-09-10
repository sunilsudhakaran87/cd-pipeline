pipeline {

    
    agent {
        label 'jenkins-slave-builder-label'
    }

    stages {       
        stage('Test it all') {
            
            steps {
                sh "type gcloud"
                sh "ls -l /"
                sh "type maven"
            }            
        }
    }
}
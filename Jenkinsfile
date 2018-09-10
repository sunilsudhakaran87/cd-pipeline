pipeline {

    
    agent {
        label 'jenkins-slave-builder-label'
    }

    stages {       
        stage('Test it all') {
            
            steps {
                container('jenkins-slave-builder') {
                        sh '''
                            pwd
                            ls -tlra /
                            ls -ltra /home
                            uname -a
                            type gcloud
                            type docker
                        '''    
                    }
            }            
        }
    }
}

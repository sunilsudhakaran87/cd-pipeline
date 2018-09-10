pipeline {

    
    agent {
        label 'jenkins-slave-builder-label'
    }

    stages {       
        stage('Test it all') {
            
            steps {
                container('jenkins-slave-builder') {
                    script {
                        dockerVersions = sh(returnStdout: true, script: '''
                            gcloud container images list-tags --format='value(TAGS)' asia.gcr.io/white-berm-210209/camel-gke | 
                            tr '\n' ',' | sed 's/,$//'
                        ''').trim()
                    }
                    
                    /*
                    script {
                        env.RELEASE_SCOPE = input message: 'User input required', ok: 'Release!',
                            parameters: [choice(name: 'RELEASE_SCOPE', choices: 'patch\nminor\nmajor', description: 'What is the release scope?')]
                    }
                    */
                }
            }            
        }
    }
}

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
                        
                        versions = dockerVersions.replaceAll(',', '\n')
                        env.dockerImageVersion = input message: 'User input required', ok: 'Select!',
                            parameters: [choice(name: 'RELEASE_SCOPE', choices: versions, description: 'Select docker image version')]
                        sh '''
                            gcloud container clusters get-credentials jenkins-cd --zone asia-east1-b --project white-berm-210209
                            retVal=$(kubectl get deployment/camel-gke -n ${targetNamespace}) || 
                                echo "Deployment does not exist for camel-gke in namespace ${targetNamespace}"
                            if [ -z "${retVal}" ] ;then
                                echo "Creating new deployment"
                                kubectl run camel-gke --image=asia.gcr.io/white-berm-210209/camel-gke:${dockerImageVersion} -n ${targetNamespace}
                            else
                                echo "Updating exising deployemnt to new image"
                                kubectl set image deployment/camel-gke camel-gke=asia.gcr.io/white-berm-210209/camel-gke:${dockerImageVersion} -n ${targetNamespace}
                            fi
                        '''
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

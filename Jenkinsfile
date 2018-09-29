pipeline {

    
    agent {
        label 'jenkins-k8s-slave-label'
    }

    stages {       
        stage('Test it all') {
            
            steps {
                container('custom-jenkins-slave') {
                    script {
                        dockerVersions = sh(returnStdout: true, script: '''
                            gcloud container images list-tags --format='value(TAGS)' \
                                    ${regionName}/${projectName}/${applicationName} | \
                                    tr '\n' ',' | sed 's/,$//'
                        ''').trim()
                        
                        versions = dockerVersions.replaceAll(',', '\n')
                        env.dockerImageVersion = input message: 'Select docker image version', ok: 'Select!',
                            parameters: [choice(name: 'DOCKER_IMAGE_VERSION', 
                                                choices: versions, description: 'Select docker image version')]
                        sh '''
                            #gcloud container clusters get-credentials \
                                #${clusterName} --zone ${zoneName} --project ${projectName}
                            
                            retVal=$(kubectl get deployment/${applicationName} -n ${targetNamespace}) || \
                                echo "Deployment does not exist for ${applicationName} in namespace ${targetNamespace}"
                            
                            if [ -z "${retVal}" ] ;then
                                echo "Creating new deployment"
                                
                                kubectl run ${applicationName} \
                                    --image=${regionName}/${projectName}/${applicationName}-gke:${dockerImageVersion} \
                                    -n ${targetNamespace}
                            
                            else
                                echo "Updating exising deployemnt to new image"
                                kubectl set image deployment/${applicationName} \
                                    ${applicationName}=${regionName}/${projectName}/${applicationName}:${dockerImageVersion} \
                                    -n ${targetNamespace}
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

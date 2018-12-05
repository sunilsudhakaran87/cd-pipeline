pipeline {

    
    agent {
        label 'image-builder'
    }

    stages {       
        stage('Trigger deployment') {
            
            steps {
                withCredentials([file(credentialsId: 'deployer-service-account', variable: 'GC_AUTH_KEY')]) {
                    container('application-image-builder') {
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
                                gcloud container clusters get-credentials \
                                    ${clusterName} --zone ${zoneName} --project ${projectName}
                                
                                retVal=$(kubectl get deployment/${applicationName} -n ${targetNamespace}) || \
                                    echo "Deployment does not exist for ${applicationName} in namespace ${targetNamespace}"
                                
                                if [ -z "${retVal}" ] ;then
                                    echo "Creating new deployment"
                                    
                                    kubectl run ${applicationName} \
                                        --image=${regionName}/${projectName}/${applicationName}:${dockerImageVersion} \
                                        -n ${targetNamespace}
                                
                                else
                                    echo "Updating exising deployemnt to new image"
                                    kubectl set image deployment/${applicationName} \
                                        ${applicationName}=${regionName}/${projectName}/${applicationName}:${dockerImageVersion} \
                                        -n ${targetNamespace}
                                fi
                                
                                svcExists=$(kubectl get svc/${applicationName} -n ${targetNamespace}) || \
                                        echo "Kubernetes service does not exist for ${applicationName} in namespace ${targetNamespace}"
                                    
                                if [ -z "${svcExists}" ] ;then
                                    echo "Creating new service"

                                    kubectl expose deployment ${applicationName} \
                                        --port=8080 -n ${targetNamespace}
                                else
                                    echo "Kubernetes service already exists for ${applicationName}"
                                fi
                            '''
                        }
                    }
                }
            }            
        }
    }
}

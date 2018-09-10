pipeline {

    
    agent {
        label 'jenkins-slave-builder-label'
    }

    stages {       
        stage('Test it all') {
            
            steps {
                container('jenkins-slave-builder') {
                    
                    script {
                        def cmd = "gcloud container images list-tags --format='json' asia.gcr.io/white-berm-210209/camel-gke"
                        o = cmd.execute()
                        o.waitFor()
                        def json = o.text

                        def js = new groovy.json.JsonSlurper()
                        def o = js.parseText(json)
                        env.array = []

                        o.each {
                            env.array.push(it.tags[0])
                        }  

                        env.array.join('\n')
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

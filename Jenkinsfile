podTemplate(containers: [
        containerTemplate(name: 'busybox', image: 'busybox', ttyEnabled: true, command: '/bin/cat'),
        containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: '/bin/cat')
    ]) {
    node (POD_LABEL) {
        withEnv([
            "AN_ACCESS_KEY=myPassword",
            "PULL_REQUEST='pr-${env.CHANGE_ID}'",
            "IMAGE_TAG='${env.PULL_REQUEST}'"
        ]){
                try{
                    stage('Summary') {
                        container('busybox') {
                          sh 'env'
                          sh script: """
                                echo "GIT_BRANCH: ${BRANCH_NAME}"
                                echo "PULL_REQUEST: ${PULL_REQUEST}"
                            """, label: "Details summary"
                        }
                    }
                    stage('Deploy') {
                        container('docker'){
                            sh "docker build -t app:${env.GIT_COMMIT} ."
                            sh "docker push app:${env.GIT_COMMIT}"
                            echo "Esto es un test"
                        }
                    }
                }catch(err){
                    throw new Exception("Error: "+err)
                }finally{
                    cleanWs()
                }
        }
    }
}

podTemplate(containers: [
        containerTemplate(name: 'busybox', image: 'busybox', ttyEnabled: true, command: '/bin/cat'),
        containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: '/bin/cat')
    ]) {
    node (POD_LABEL) {
        withEnv([
            "AN_ACCESS_KEY=myPassword",
            "IMAGE_TAG='${env.BUILD_ID}'"
        ]){
                try{
                    stage('Summary') {
                        container('busybox') {
                          sh 'env'
                          sh script: """
                                echo "GIT_BRANCH: ${JOB_NAME}"
                                echo "PULL_REQUEST: ${IMAGE_TAG}"
                            """, label: "Details summary"
                        }
                    }
                    stage('Deploy') {
                        container('docker'){
                            sh "docker build -t app:${IMAGE_TAG} ."
                            sh "docker push app:${IMAGE_TAG}"
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

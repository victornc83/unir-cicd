def yaml="""
apiVersion: v1
kind: Pod
spec:
  imagePullSecrets: 
  - name: botjenkins
  securityContext:
    fsGroup: 0
  containers:
  - name: busybox
    image: busybox
    tty: true
    command:
    - "cat"
  - name: docker
    image: docker
    tty: true
    command:
    - "cat"
    volumeMounts:
    - name: dockersock
      mountPath: "/var/run/docker.sock"
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
podTemplate(showRawYaml: false, yaml: yaml){
    node(POD_LABEL){
        withEnv([
            "AN_ACCESS_KEY=myPassword",
            "IMAGE_TAG='${env.BUILD_ID}'"
        ]){
                try{
                    stage('Summary') {
                        container('busybox') {
                          checkout scm
                          sh 'env'
                          sh script: """
                                echo "GIT_BRANCH: ${JOB_NAME}"
                                echo "PULL_REQUEST: ${IMAGE_TAG}"
                            """, label: "Details summary"
                        }
                    }
                    stage('Deploy') {
                        container('docker'){
                            sh "docker build -t victornc83/unir-app:${IMAGE_TAG} ."
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

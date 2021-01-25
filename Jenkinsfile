pipeline {
    agent {
        label 'docker'
    }

    options {
        timeout(time: 1, unit: 'HOURS')
    }

    triggers {
        cron('H */1 * * 1-5')
        pollSCM('H/10 * * * 1-5')
    }

    environment {
        AN_ACCESS_KEY = credentials('my-predefined-secret-text')
        PULL_REQUEST = "pr-${env.CHANGE_ID}"
        IMAGE_TAG = "${env.PULL_REQUEST}"
    }

    stages {
        stage('Summary') {
            steps {
                sh script: """
                    echo "GIT_BRANCH: ${GIT_BRANCH}"
                    echo "PULL_REQUEST: ${PULL_REQUEST}"
                """, label: "Details summary"
            }
        }
        stage('Deploy') {
            agent {
                label "docker"
            }
            when { 
                beforeAgent true
                allOf {
                    equals expected: 'master', actual: BRANCH_NAME
                    equals expected: 'SUCCESS', actual: currentBuild.currentResult
                }
            }
            steps {
                unstash name: "DIST"
                sh "docker build -t app:${env.GIT_COMMIT} ."
                sh "docker push app:${env.GIT_COMMIT}"
                script {
                    echo "Esto es un test"
                }
            }
        }
    }

    post {
        success {
            emailext subject: "Pipeline successful", body:"Todo fue OK.", to: "victornc83@gmail.com"
            cleanWs()
        }
        unstable {
            emailext subject: "Pipeline tests not successful", body:"La ejecución fue inestable.",to: "victornc83@gmail.com"
            cleanWs()
        }
        failure {
            emailext subject: "Pipeline error", body: "Algo fue muy mal!!" ,to: "victornc83@gmail.com"
            cleanWs()
        }
    }
}

node {
    // Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone') {
            checkout scm
        }
        stage ('Build') {
                git url: 'https://github.com/tejaavrsk/helm_test.git'
        }
        stage ('Tests') {
            parallel 'static': {
                sh "whoami"
            },
            'unit': {
                sh "pwd"
            },
            'integration': {
                sh "echo 'shell scripts to run integration tests...'"
            }
        }
        stage ('Deploy in Testing Environment') {
            sh '''
              . ./helm-init.sh
              helm install
            '''
        }
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}

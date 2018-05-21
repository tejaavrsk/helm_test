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
        stage ('Deploy in Testing Environment') {
            sh '''
              sudo -H -u hekujen bash -c 'helm upgrade --install --namespace testing nginx . '
            '''  
            timeout(time: 1, unit: 'HOURS') {
               input message: "Does Pre-Production look good?"
            }
        }
        stage ('Deploy in Prod Environment') {
            sh '''
              sudo -H -u hekujen bash -c 'helm upgrade --install --namespace production nginx . ' 
            '''
        }
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}

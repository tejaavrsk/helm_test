pipeline {
    // Clean workspace before doing anything
    agent any

    try {
        stage ('Clone') {
            checkout scm
        }
        stage ('Build') {
                git url: 'https://github.com/tejaavrsk/helm_test.git'
        }
        stage ('Tests') {
            environment {
                CHARTNAME = 'helm_test'
            }

            parallel 'Syntax Checks': {
                sh "echo 'shell scripts to run static tests...'"
            },
            'Security Scans': {
                sh "echo ' $CHARTNAME shell scripts to run Security Scans' "
            },
            'Helm Linting': {
                sh '''
                  mkdir -p /tmp/$CHARTNAME
                  cp -Rp . /tmp/$CHARTNAME
                  sudo -H -u hekujen bash -c 'helm lint /tmp/$CHARTNAME'
                  rm -f /tmp/$CHARTNAME
                '''
            }
        }
        stage ('Deploy in Testing Environment') {
            sh '''
              sudo -H -u hekujen bash -c 'helm upgrade --install --namespace testing helm-testing . '
            '''  
            timeout(time: 1, unit: 'HOURS') {
               input message: "Does Pre-Production look good?"
            }
        }
        stage ('Deploy in Prod Environment') {
            sh '''
              sudo -H -u hekujen bash -c 'helm upgrade --install --namespace production helm-prod . ' 
            '''
        }
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}

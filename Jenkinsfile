pipeline {
    // Clean workspace before doing anything
    agent any
    stages {
        stage ('Clone') {
            steps {
            checkout scm
            }
        }
        stage ('Build') {
            steps {
                git url: 'https://github.com/tejaavrsk/helm_test.git'
            }
        }
        stage ('Tests') {
            environment {
                CHARTNAME = 'helm_test'
            }
            steps {
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
        }
        stage ('Deploy in Testing Environment') {
            steps {
              sh '''
                sudo -H -u hekujen bash -c 'helm upgrade --install --namespace testing helm-testing . '
              '''
              timeout(time: 1, unit: 'HOURS') {
                 input message: "Does Pre-Production look good?"
              }
            }
        }
        stage ('Deploy in Prod Environment') {
            steps {
              sh '''
                sudo -H -u hekujen bash -c 'helm upgrade --install --namespace production helm-prod . '
              '''
            }
        }
    }
}

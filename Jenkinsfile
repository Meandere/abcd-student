pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-token', url: 'https://github.com/Meandere/abcd-student', branch: 'main'
                }
            }
        }
        stage('Example') {
            steps {
                echo 'Hello!'
                sh 'ls /var/jenkins_home/workspace/Test1/.zap/'
                sh 'chmod -R 777 ${WORKSPACE}/nowyzap/'
                sh 'chown -R ${whoami):${whoami) ${WORKSPACE}/nowy/zap || true'
            }
        }
        stage('[ZAP] Baseline passive-scan') {
            steps {
                sh 'mkdir -p results/'
                sh '''
                docker run --name juice-shop2 -d --rm -p 3000:3000 bkimminich/juice-shop
                sleep 5
                '''
                sh '''
                docker run --name zap2 \
                --add-host=host.docker.internal:host-gateway \
                -v ${WORKSPACE}/nowyzap/:/zap/wrk/:rw \
                -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/zap.yaml" \
                || true
                '''
             }
          }
     }
    post {
        always {
            sh '''
                docker stop zap2 juice-shop2
                docker rm zap2
            '''
        }
    }
}

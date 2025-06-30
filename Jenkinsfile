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
                sh 'docker stop zap2 juice-shop2'
                sh 'docker rm zap2 juice-shop2'
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
                -v ${WORKSPACE}/.zap/:/zap/wrk/:rw \
                -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml" \
                || true
                '''
             }
          }
     }
    post {
        always {
            sh '''
                docker cp zap2:/zap/wrk/reports/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
                docker cp zap2:/zap/wrk/reports/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
                docker stop zap2 juice-shop2
                docker rm zap2
            '''
        }
    }
}

pipeline {
    agent any
    options {
        timeout(time: 5, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    environment {
        project = 'expense'
        component = 'backend'
        awsECRurl = 'dkr.ecr.us-east-1.amazonaws.com'
        awsRegion = 'us-east-1'
        awsCreds = 'aws-creds'
        awsID = '339712874850'
        appVersion = ''
        imageURL = ''
        ENV = ''
    }
    parameters{
        choice(name: 'ENV', choices: ['dev', 'qa', 'uat', 'pre-prod'], description: 'Select environment for deploy')
        string(name: 'version', description: 'Enter app version')
    }
    stages {
        stage('Setup Environment') {
            steps {
                script {
                    ENV = params.ENV
                    appVersion = params.version
                    imageURL = "${awsID}.${awsECRurl}/${project}/${ENV}/${component}"
                }
            }
        }
        stage('Deploy') {
            steps {
                withAWS(region: "${awsRegion}", credentials: "${awsCreds}") {
                    sh """
                        aws eks update-kubeconfig --region ${awsRegion} --name ${project}-${ENV}
                        cd helm
                        sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${ENV}.yaml
                        helm upgrade --install ${component} -f values-${ENV}.yaml .
                    """
                }
            }
        }
    }
    post {
        always {
            deleteDir()
        }
    }
}

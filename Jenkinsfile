#!/usr/bin/env groovy


AWS_DEFAULT_REGION = 'us-east-1'
POLL_INTERVAL = 1000
DURATION = 3600
ROLE_NAME = 'jenkins-deployer-role'
STACK_NAME = 'remediate-public-facing-sg-1'
TEMPLATE_FILE = 'remediate-public-facing-sg.yaml'
PROJECT_NAME = 'remediate-public-facing-sg-1'

// parameters don't work in scripted (always null)

node {

    stage('Checkout sources') {
        checkout scm // Checks out the repo where the Jenkinsfile is located
    }

    // This stage validates the cloudformation templates using cfn-lint
    stage('Validate') {

        environment {
            ARTEFACT_BUCKET_NAME = credentials('artefact-bucket-name')
            EXTERNAL_ID = credentials('jenkins-external-id')
        }

        withCredentials([string(credentialsId: 'artefact-bucket-name', variable: 'ARTEFACT_BUCKET_NAME'),
            string(credentialsId: 'jenkins-external-id', variable: 'EXTERNAL_ID')]) {

            withAWS(region:"${AWS_DEFAULT_REGION}") {
                s3Upload(file:"${TEMPLATE_FILE}", bucket:"${env.ARTEFACT_BUCKET_NAME}", path:'template/')
                script {
                    sh """
                        echo "@@@@@@@@@@"
                        echo "${ARTEFACT_BUCKET_NAME}"
                        echo "@@@@@@@@@@"
                        aws cloudformation validate-template --template-url \
                        https://${env.ARTEFACT_BUCKET_NAME}.s3.${AWS_DEFAULT_REGION}.amazonaws.com/template/${TEMPLATE_FILE}
                    """
                    
                }
            }
        }
    }

    // Create deploy package 
    stage('Build') {
        
        environment {
            ARTEFACT_BUCKET_NAME = credentials('artefact-bucket-name')
            EXTERNAL_ID = credentials('jenkins-external-id')
        }

        withCredentials([string(credentialsId: 'artefact-bucket-name', variable: 'ARTEFACT_BUCKET_NAME'),
            string(credentialsId: 'jenkins-external-id', variable: 'EXTERNAL_ID')]) {

            script {
                sh """
                    aws cloudformation package --template-file ${TEMPLATE_FILE} --s3-bucket ${ARTEFACT_BUCKET_NAME} \
                    --output-template-file package.yaml
                    
                """
            }
        }
    }

    // This stage is deployed on the branch
    stage('Deploy to sandpit') {
        if ("${BRANCH_NAME}" != 'main') {  

            cfnUpdater("602011150591", "ExternalID=ONRwtuyXNwaGMQArd3zBI6Zk5fMwMGrQ")

        }
    }
}
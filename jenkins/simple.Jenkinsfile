
pipeline{
    agent any

    stages {
        stage('Get  repo'){
                steps{
                    echo 'Getting repo...'
                    script {
                        checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: 'https://github.com/AlexUD/HelloCi.git']]])
                    }
                }
        }
        stage('Build Archive'){
            steps{
                zip (
                    dir: 'CloudFormation/iac',
                    glob: '**/*',
                    zipFile: 'CloudFormation/iac.zip',
                    overwrite: true
                )
            }
        }
        stage('Deploy'){
            steps{
                echo 'Deploying...'
                withAWS(region:'us-west-2',credentials:'aws-admin-alex-cred') {
                    sh 'echo "Uploading content with AWS creds..."'
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'CloudFormation/iac.zip', bucket:'codepipeline.test.templates/artifacts')
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'CloudFormation/DeliveryPipeline.yaml', bucket:'codepipeline.test.templates')
                }
            }
        }
    }
}
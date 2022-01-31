
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
                zip dir: 'CloudFormation',
                    glob: '**/*',
                    zipFile: 'CloudFormation/iac.zip'
                )
            }
        }
        stage('Build'){
            steps{
                echo 'Building...'
                sh 'mvn clean install'
            }
        }
        stage('Deploy'){
            steps{
                echo 'Deploying...'
                withAWS(region:'us-west-2',credentials:'aws-admin-alex-cred') {
                    sh 'echo "Uploading content with AWS creds..."'
                    s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'target/HelloCi-1.0-SNAPSHOT.jar', bucket:'jenkins.testbucket.builds')
                }
            }
        }
    }
}
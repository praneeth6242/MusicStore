pipeline {
    agent { label 'JAVA_NODE1' }
    triggers { pollSCM ('* * * * *') } 
    parameters {
        choice(name: 'DOTNET_GOAL', choices: ['restore','build','package', 'install', 'clean'], description: 'Maven Goal')
    }
    stages {
        stage('vcs') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/khajadevopsmarch23/MusicStore.git'
            }
        }
        stage('build') {
            steps {
                sh 'dotnet restore ./MusicStore/MusicStore.csproj && dotnet build ./MusicStore/MusicStore.csproj'
            }
        }
        stage('copy build') {
            steps {
                sh "mkdir -p /tmp/archive/$JOB_NAME/${BUILD_ID} && cp ./target/*.csproj /tmp/archive/$JOB_NAME/${BUILD_ID}/" 
                sh "aws s3 sync /tmp/archive/$JOB_NAME/${BUILD_ID}/  s3://nagaspring/"
            }
        } 
        stage('test') {
            steps {
                sh 'dotnet test --logger "junit;LogFilePath=TEST-musicstoretest.xml" ./MusicStoreTest/MusicStoreTest.csproj'
                junit testResults: '**/TEST-*.xml'
            }
        } 
       }
    post {
      always {
         mail subject: 'the build ${JOB_NAME} with ${BUILD_ID} is build',
              body: 'build is either success or fail',
              from: 'praneeth@gmail.com',
              to: 'poorna@gmail.com'
      }
   }   
}
pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:202051196'
       appRegistry = "350936383096.dkr.ecr.ap-south-1.amazonaws.com/202051196_capstone_project"
       capstoneRegistry = "https://350936383096.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "202051196_capstone_cluster"
       service = "202051196_capservice"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/MukVai/202051196_capstone'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: '202051196', region: 'ap-south-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

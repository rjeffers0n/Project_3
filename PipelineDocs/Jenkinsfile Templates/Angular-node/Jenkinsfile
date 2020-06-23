pipeline{

    agent any

    environment{
        //REPOSITORY PROJECTNAME - SEE CONVENTION FOR CALIBUR:
        //caliber-assessment
        //caliber-quality
        //caliber-config
        //caliber-category
        //caliber-batch
        RegisterFilename = "CHANGEME"
        //THE NAME OF THE DOCKER ECR REPOSITORY  ====  DO NOT CHANGE ====
        Register ="367484709954.dkr.ecr.us-east-2.amazonaws.com/${RegisterFilename}"
        //THE JENKINS CREDENTIAL ID TO MATCH ECR REPOSITORY CREDENTIALS   ====  DO NOT CHANGE ====
        RegisterCredential ="RevatureECR"
        dockerImage = ""
        Region = 'ecr:us-east-2'
    }
  stages{

    stage('Install/update Devkit/GetNodeVersion'){
        steps
            {
                sh 'npm install @angular-devkit/build-angular'
                sh 'node --version'
            }
        }
      stage(' Build'){
            
            steps{
                  sh 'ng build'                
                }
          }
   stage('Docker Build')
    {
        steps{
            script
            {
              dockerImage = docker.build("${Register}")
              echo "${dockerImage}"
            }
        }
    }
  
   stage ('Push to ECR')
    {

        steps
        {
            script{

              docker.withRegistry('https://367484709954.dkr.ecr.us-east-2.amazonaws.com', "${REGION}:${RegisterCredential}")
                {
                    dockerImage.push("latest")
                }
            }  
        }
    }
    stage ("Remove docker images"){
        steps
        {
          sh "docker image prune"
        }
      }
    }
    post {
      failure {
          emailext (
              subject: "FAILED: '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
              body: "JOB '${env.JOB_NAME} [${env.BUILD_NUMBER}]' has failed on '${env.BUILD_TIMESTAMP}'.\nGIT URL: '${env.GIT_URL}'\nGIT BRANCH: '${env.GIT_BRANCH}'\nGIT COMMIT SHA: '${env.GIT_COMMIT}'\nCheck the console output at '${env.BUILD_URL}'.",
              to: "centerofexcellence@revature.com",
              attachLog: true
              )
      }
    }
}


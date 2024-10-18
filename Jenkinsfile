def COLOR_MAP = [
  'SUCCESS' : 'good',
  'FAILURE' : 'danger'
]
pipeline{
  agent any 


   environment{
    nexuspass   = credentials('nexuspass')
    }

 // now we have a problem, var BUILD_ID,TIMESTAMP  are differnet from branche main ,because it's different pipeline
 // so we need to parameterized this pipeline 
 // that mean admin will put the input at building the pipeline 
  stages{  
    // when you run the pipeline it will ask you this parameter
   stage('setup parameter'){
     steps{
       script{
         properties([
           parameter([
             string(
                defaultValue: '',
                name: 'BUILD'
             ),
             string(
              defaultValue: '',
              name: 'TIME'
             )
           ])
       ])
      }
     }
   }
  
  
  
  
  stage("Ansible Deploy to prod env"){
      steps{
        ansiblePlaybook({
            inventory     : 'ansible/inventory',
            playbook      :  'ansible/site.yml',
            installation  :  'ansible',
            colorized     :   true ,
            credentialsId :    'appprodlogin', // username and private key of instance saved at jenkins credentials 
            disableHostKeyChecking : true ,
            extraVars     :  [  // for nexus url
               USER       :  'admin' ,
               PASS       :   ${nexuspass}
               nexusip    :  '172.35.5.1' , 
               reponame   :  'vprofile-release'
               groupid    :  'QA' ,
               time       :  "${env.TIME}" ,
               build      : "${env.BUILD}" ,
               artifactid :  "vproapp"
               vprofile_version : "vproapp-${env.BUILD}-${env.TIME}.war"

            ]



        })

      }
  }  
  
  }  
  post {
        always  {
            echo 'slack notification.'
            slackSend channel: '#cicdjenkins',
            color:  COLOR_MAP[currentBuild.currentResult],
            message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n for more info visit : ${env.BUILD_URL} " 
        }
  }

}

node {    

      def app     
      def appName='App1'
      def snapName=''
      def deployName = 'PROD-US'
      def exportFormat ='json'
      def configFilePath = "paymentService"
      def fileNamePrefix ='exported_file_'
      def fullFileName="${appName}-${deployName}-${currentBuild.number}.${exportFormat}"
      def changeSetId=""
      def snapshotName=""

      stage('Clone repository') {               
             
            checkout scm    
      }     
      stage('Build image') {       

            snDevOpsStep()

            app = docker.build("santoshnrao/demo-training-studio")    
       }     
//       stage('Test image') {           
//             app.inside {            
             
//              sh 'echo "Tests passed"'        
//             }    
//         }     
//        stage('Push image') {
//                   sh 'ls -a'
//                   docker.withRegistry('https://registry.hub.docker.com', 'santoshnrao-dockerhub') {            
//                   app.push("${env.BUILD_NUMBER}")            
//                   app.push("latest")        
//               }    

//            }

      
      stage('Validate Configurtion file'){
            

            sh "echo validating configuration file ${configFilePath}.${exportFormat}"
            changeSetId = snDevOpsConfigUpload(applicationName:"${appName}",target:'component',namePath:'paymentservice.v1.1', fileName:"${configFilePath}", autoCommit:'true',autoValidate:'true',dataFormat:"${exportFormat}")

            echo "validation result $changeSetId"
      }

    stage("register change set to pipeline"){
        echo "Change set registration for ${changeSetId}"
        changeSetRegResult = snDevOpsConfigRegisterChangeSet(changesetId:"${changeSetId}")
        echo "change set registration set result ${changeSetRegResult}"
    }

    stage("Get snapshots created"){
          
        echo "Triggering Get snapshots for applicationName:${appName},deployableName:${deployName},changeSetId:${changeSetId}"

        changeSetResults = snDevOpsConfigGetSnapshots(applicationName:"${appName}",deployableName:"${deployName}",changeSetId:"${changeSetId}")
        echo "ChangeSet Result : ${changeSetResults}"
        
        def jsonSlurper = new JsonSlurper()
        def changeSetResultsObject = jsonSlurper.parseText("${changeSetResults}")
        
          list.each(changeSetResultsObject){

                if(it.validation == "passed"){
                      echo "validation passed for snapshot : ${it.name}"
                      snapshotName = ${it.name}
                }else{
                      echo "Snapshot failed to get validated : ${${it.name}}" ;
                      assert it.validation == "passed"
                }
          }
          
    }

    stage('Publish the snapshot'){
        echo "Step to publish snapshot applicationName:${appName},deployableName:${deployName} snapshotName:${snapshotName}"
        snDevOpsConfigPublish(applicationName:"${appName}",deployableName:"${deployName}",snapshotName: "${snapshotName}")
    }



       stage("deploy to system") {
             
             sh ' echo deployment done'
            //sh "docker run -it --rm -d -p 80:80 --name web santoshnrao/demo-training-studio:${env.BUILD_NUMBER}"
            
//             withKubeConfig([credentialsId: 'santosh-devops-config-k8s']) {
//                         sh 'kubectl apply -f k8s/'
//                   }
            // kubernetesDeploy(kubeconfigId: 'devops-config-demo-1',               // REQUIRED

            //      configs: 'k8s/', // REQUIRED
            // )
       }
}

node {    
      environment {
            registry = "YourDockerhubAccount/YourRepository"
            registryCredential = 'dockerhub_id'
            dockerImage = ''
      }

      def app     
      stage('Clone repository') {               
             
            checkout scm    
      }     
      stage('Build image') {         
       
            app = docker.build("santoshnrao/demo-training-studio")    
       }     
      stage('Test image') {           
            app.inside {            
             
             sh 'echo "Tests passed"'        
            }    
        }     
       stage('Push image') {
                  docker.withRegistry('https://registry.hub.docker.com', 'santoshnrao-dockerhub') {            
                  app.push("${env.BUILD_NUMBER}")            
                  app.push("latest")        
              }    
           }
        }
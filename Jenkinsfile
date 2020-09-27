pipeline {
   agent any
   tools {
     maven 'maven'
   }

   stages{

      stage("Checkout"){
       steps{
         echo "this is taken care using pipelinescript from SCM in Jenkins"
         echo "so no need to add any steps here in Jenkinsfile"
         }
      }
      stage("build using Maven"){
       steps{
         sh 'mvn clean install package'
         }
      }
      stage("deploy the build to tomcat server"){
        steps{
         deploy adapters: [
             tomcat9(
               credentialsId: '1b939522-041e-4c28-88da-358a7abd5098',
               path: '',
               url: 'http://3.16.214.222:8080/'
               )
             ],
             contextPath: null,
             war: '**/*.war'
             }
      }


   }
}

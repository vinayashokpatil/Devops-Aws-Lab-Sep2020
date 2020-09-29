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
      stage("Build using Maven"){
       steps{
         sh 'mvn clean install package'
         }
      }
      stage("Publish the artifacts to Nexus"){
        steps{

      }


   }
}
}

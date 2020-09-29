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
           script{
            def mavenpom = readMavenPom file: 'pom.xml'
            nexusArtifactUploader artifacts: [
            [
             artifactId: 'VinayDevOpsLab',
             classifier: '',
             file: "target/${ artifactId }-${ version }.war",
             type: 'war']
             ],
             credentialsId: 'nexus3',
             groupId: 'lu.amazon.aws.demo',
             nexusUrl: '172.31.9.39:8081',
             nexusVersion: 'nexus3',
             protocol: 'http',
             repository: 'devops-aws-lab',
             version: "{ version }"
      }
      }


   }
}
}

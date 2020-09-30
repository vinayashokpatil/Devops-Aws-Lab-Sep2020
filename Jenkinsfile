pipeline {
   agent any
   tools {
     maven 'maven'
   }

   environment {
     //Use Pipeline Utility Steps plugin to read information from pom.xml into env variables
      IMAGE = readMavenPom().getArtifactId()
      VERSION = readMavenPom().getVersion()
     }

   stages{

      stage("Checkout"){
       steps{
         echo "IMANGE is '${IMAGE}'"
         echo "VERSION is '${VERSION}'"
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
             try {
                def project = readMavenPom file: 'pom.xml'


                nexusArtifactUploader artifacts: [
                [
                artifactId: "${project.artifactId}",
                classifier: '',
                file: "target/${project.artifactId}-${project.version}.war",
                type: 'war']
                ],
                credentialsId: 'nexus3',
                groupId: 'lu.amazon.aws.demo',
                nexusUrl: '172.31.9.39:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'devops-aws-lab',
                version: "${project.version}"
               }
             catch(Exception e) {
                 echo "repository artifact version already exists"
             }

             }
           }
      }

      stage("Publish the artifacts to ansible controller machine"){

      when {
          expression {
          return "${project.name}" == 'RELEASE';
          }
       }
        steps{
            sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks//artifacts-from-jenkins', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
           }
        }


    stage("Invoke the ansible playbook hosted on ansible controller"){
      steps{
         sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/deploywar-to-tomcat.yaml -i /opt/playbooks/hosts', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
     }
    }

}
}

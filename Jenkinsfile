pipeline {

   agent any

   tools {
     maven 'maven'
   }

   environment {
     //Use Pipeline Utility Steps plugin to read information from pom.xml into env variables
      ArtifactID = readMavenPom().getArtifactId()
      Version = readMavenPom().getVersion()
      Name = readMavenPom().getName()
      Release = ''
    }

   stages{


      stage("Checkout"){
        steps{
         echo "ArtifactID is '${ArtifactID}'"
         echo "Version is '${Version}'"
         echo "Name is '${Name}'"
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
        script {
          def NexusRepo = Version.endsWith("SNAPSHOT") ? "devops-aws-lab-SNAPSHOT" : "devops-aws-lab-RELEASE"
          def AnsiblePublish = Version.endsWith("SNAPSHOT") ? "SNAPSHOT" : "RELEASE"
          Release = AnsiblePublish

                nexusArtifactUploader artifacts: [
                [
                artifactId: "${ArtifactID}",
                classifier: '',
                file: "target/${ArtifactID}-${Version}.war",
                type: 'war']
                ],
                credentialsId: 'nexus3',
                groupId: 'lu.amazon.aws.demo',
                nexusUrl: '172.31.9.39:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: "${NexusRepo}",
                version: "${Version}"

           }
        }
      }

      stage("Publish the artifacts to ansible controller machine"){

         when {
             environment name: 'Release', value: 'RELEASE'
        }

        steps{
            echo "Release is '${Release}'"
            sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks//artifacts-from-jenkins', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
           }
       }


    stage("Invoke the ansible playbook hosted on ansible controller"){

     when {
          environment name: 'Release', value: 'RELEASE'
        }

      steps{
         sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/deploywar-to-tomcat.yaml -i /opt/playbooks/hosts', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
     }
    }
}
}

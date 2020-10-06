/* All valid Declarative Pipelines must be enclosed within a pipeline block */

pipeline {

   /* The agent section specifies where the entire Pipeline, or a specific stage, will execute in the Jenkins environment depending on where the agent section is placed. The section must be defined at the top-level inside the pipeline block, but stage-level usage is optional. */

   agent any

   /*A section defining tools to auto-install and put on the PATH. This is ignored if agent none is specified.*/
   /* supported tools are maven,jdk and gradle */

   tools {
     maven 'maven'
   }

  /*environment variables */

   environment {
      // Use Pipeline Utility Steps plugin to read information from pom.xml into env variables

      ArtifactID = readMavenPom().getArtifactId()
      Version = readMavenPom().getVersion()
      Name = readMavenPom().getName()
      Release = ''
    }

  /* stages -> stage -> steps */

   stages{
      stage("SCM Checkout"){
          steps{
            echo "added development branch"
            echo 'Pulling from branch ... ' + env.GIT_BRANCH
            echo "ArtifactID is '${ArtifactID}'"
            echo "Version is '${Version}'"
            echo "Name is '${Name}'"
            echo "The stage SCM checkout is taken care automatically by pipelinescript from SCM in Jenkins"
            echo "so no need to add any steps here in the Jenkinsfile"
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
               /*The script step takes a block of Scripted Pipeline and executes that in the Declarative Pipeline. For most use-cases, the script step should be unnecessary in Declarative Pipelines, but it can provide a useful "escape hatch." script blocks of non-trivial size and/or complexity should be moved into Shared Libraries instead. */
               /* Here varibale NexusRepo is defined and with the help of script and ternary or conditional operator I am setting the value to the name of the nexus repository */

               def NexusRepo = Version.endsWith("SNAPSHOT") ? "devops-aws-lab-SNAPSHOT" : "devops-aws-lab-RELEASE"
               def AnsiblePublish = Version.endsWith("SNAPSHOT") ? "SNAPSHOT" : "RELEASE"

               /* Here I am setting the value of environment variable Release */
               Release = AnsiblePublish

               /* Install the Nexus Artifact Uploader plug in to generate below snippet */
               /* I am uploading the artifacts to Nexus artifacts repository */

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

      stage("print the name of Release global variable") {
        steps{
           echo "Release is '${Release}'"
        }
      }

      stage("Publish the artifacts to ansible controller machine"){

       /* execute the steps only if the build version is a RELEASE candidate */
         when {
             equals expected: "RELEASE", actual: "${Release}"
        }

        steps{
              /* Install Publish Over SSH plugin to generate below snippet */
              sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks//artifacts-from-jenkins', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
           }
       }


    stage("Invoke the ansible playbook hosted on ansible controller"){

     /* execute the steps only if the build version is a RELEASE candidate */
     when {
          equals expected: "RELEASE", actual: "${Release}"
        }

      steps{
         sshPublisher(publishers: [sshPublisherDesc(configName: 'ansible_controller_instance', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/deploywar-to-tomcat.yaml -i /opt/playbooks/hosts', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
  }
}

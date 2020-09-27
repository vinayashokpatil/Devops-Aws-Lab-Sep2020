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
      stage("Publish the artifacts to ansible controller machine"){
        steps{
            sshPublisher(
               publishers: [
                 sshPublisherDesc(
                   configName: 'ansible_controller_instance',
                   transfers: [
                     sshTransfer(
                        cleanRemote: false,
                        excludes: '',
                        execCommand: '',
                        execTimeout: 120000,
                        flatten: false,
                        makeEmptyDirs: false,
                        noDefaultExcludes: false,
                        patternSeparator: '[, ]+',
                        remoteDirectory: '/artifactsfromjenkins-warfiles',
                        remoteDirectorySDF: false,
                        removePrefix: '',
                        sourceFiles: '**/*.war'
                        )
                      ], usePromotionTimestamp: false,
                      useWorkspaceInPromotion: false,
                      verbose: false
                    )
                  ]
                )
      }


   }
}
}

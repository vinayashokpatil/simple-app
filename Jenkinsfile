pipeline {

/* you need to specify which agent the pipeline has to be executed against */
/* this can be master jenkins or slave or any */

          agent any

/* the below snippet will include the maven binary in to the path */
/* it is generated by logging in to AWS jenkins console -> create a demo pipeline newitem -> demo -> pipleline -> ok */
/* click on pipeline -> pipeline syntax -> Declarative Directive Generator -> select : tools: Tools -> add -> maven */
/* click Generate Directive Generator */

          tools {
             maven 'maven'
           }
          environment {
            MAVEN_OPTS = ' -Dmaven.test.failure.ignore=false'
           }

              stages{
               /* generally there are 3 stages, 1. 'Git Checkout' - pull out code from github or any scm */
               /* then stage 2 : Build using maven and then stage 3 : */

               stage('Build'){
                   steps{

                      /* click on snippet generator -> sh:Shell Script -> type "mvn clean package" -> Generate pipeline script */
                      /* copy paste below as shows sh 'mvn clean package' */
                         sh 'mvn clean package'
                        }
                     }
               stage('Upload War to Nexus'){
                   steps{

                              nexusArtifactUploader artifacts: [
                                 [
                                  artifactId: 'simple-app',
                                  classifier: '',
                                  file: 'target/simple-app-3.0.0.war',
                                  type: 'war'
                                  ]
                                ],
                                  credentialsId: 'nexus3',
                                  groupId: 'in.javahome',
                                  nexusUrl: '172.31.9.39:8081',
                                  nexusVersion: 'nexus3',
                                  protocol: 'http',
                                  repository: 'simple-app2',
                                  version: '3.0.0'

                     }

                }
               stage('Run the ansible playbook to copy file from nexus'){
                 steps{
                        sshPublisher(
                         publishers:
                          [sshPublisherDesc(
                            configName: 'ansible_controller_instance',
                            transfers: [
                              sshTransfer(
                               cleanRemote: false,
                               excludes: '',
                               execCommand: 'ansible-playbook /opt/playbooks/copywarfilefromnexustocontroller.yaml -i /opt/playbooks/hosts',
                               execTimeout: 120000,
                               flatten: false,
                               makeEmptyDirs: false,
                               noDefaultExcludes: false,
                               patternSeparator: '[, ]+',
                               remoteDirectory: '//opt/playbooks',
                               remoteDirectorySDF: false,
                               removePrefix: '',
                               sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

                }

              }


               }

          }

pipeline {
  agent any
 
      stages {
              stage('Build Artifact') {
                    steps {
                      sh "mvn clean package -DskipTests=true"
                      archive 'target/*.jar' 
                    }
                                      }  
                  // fin stage 1
                                
                stage('test unitaire ') {
                    steps {
                      catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                           sh "mvn test"                                        
                                                                                 }
                      
                          }
                            post{
                          always{
                            junit 'target/surefire-reports/*.xml'
                          
                          }
                        }



                                        }
                  // fin stage 2
            
              stage('Mutation Tests - PIT') {
              steps {
                      catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh "mvn org.pitest:pitest-maven:mutationCoverage"
                                                                                }

                    }
                                            }
                      // fin stage 3
              stage('Vulnerability Scan - Docker') {
            steps {
              catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh "mvn dependency-check:check"
              }
            }
            post{
              always{
               dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
                jacoco(execPattern: 'target/jacoco.exec')
              }
            }
           }






                      // fin stage 4

              stage('Docker Build and Push') {
                    steps {
                    withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD_', variable: 'DOCKER_HUB_PASSWORD')]) {
                    sh 'sudo docker login -u thib432 -p $DOCKER_HUB_PASSWORD'
                    sh 'printenv'
                    sh 'sudo docker build -t thib432/devops-app:""$GIT_COMMIT"" .'
                    sh 'sudo docker push thib432/devops-app:""$GIT_COMMIT""'
                                                                                                         }
 
                         }
                                              }
                      // fin stage 5
              stage('Deployment Kubernetes') {
                  steps {
                  withKubeConfig([credentialsId: 'KuberneteThib']) {
                  sh "sed -i 's#replace#thib432/devops-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                  sh "sudo kubectl apply -f k8s_deployment_service.yaml"
                                                            }
                    }
                    }
                    // fin stage 6





              }
        // fin stages
        }
        // fin pipeline
pipeline {
  agent any
 
      stages {
              stage('Build Artifact') {
                    steps {
                      sh "sudo mvn clean package -DskipTests=true"
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
                  // fin stage test unitaire
            
              stage('Mutation Tests - PIT') {
              steps {
                      catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        sh "sudo mvn org.pitest:pitest-maven:mutationCoverage"
                                                                                }

                    }
                      post{
                always{
                pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
                      }
                          }

                                            }
                      // fin stage Mutation

      stage('scan sonarqube') {
              steps {
 
            withCredentials([string(credentialsId: 'sonar', variable: 'sonar')]) {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
 
          sh "sudo mvn clean verify sonar:sonar \
          -Dsonar.projectKey=appjava \
          -Dsonar.projectName='appjava' \
          -Dsonar.host.url=http://formationthibaut.eastus.cloudapp.azure.com:9980 \
          -Dsonar.token=$sonar"
 
 
                }
              }
            }
            
        
            }
 





              
              
              stage('Vulnerability Scan - Docker') {
            steps {
              catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh "sudo mvn dependency-check:check"
              }
            }
            post{
              always{
               dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
                jacoco(execPattern: 'target/jacoco.exec')
              }
            }
           }

                      // fin stage Vulnerability

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
                      // fin stage Docker Build

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
        // test
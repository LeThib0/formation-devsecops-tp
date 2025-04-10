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
                      sh "mvn test"
                          }
                                        }
                  // fin stage 2
            
              stage('Mutation Tests - PIT') {
              steps {
                sh "mvn org.pitest:pitest-maven:mutationCoverage"
                    }
                                            }
                      // fin stage 3

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
                      // fin stage 4

        }
        // fin stages
        }
        // fin pipeline
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



        }
        // fin stages
        }
        // fin pipeline
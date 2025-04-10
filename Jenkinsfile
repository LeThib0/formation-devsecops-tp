pipeline {
  agent any
  stages{
 //------------------------------------------------
  stage('Generate Local Tag') {
    steps {
        script {
            def shortHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
            def timestamp = new Date().format('yyyyMMddHHmmss')
            env.LOCAL_TAG = "v${timestamp}-${shortHash}"

            echo "Generated Local Tag: ${env.LOCAL_TAG}"
        }
    }
    }

 //------------------------------------------------

    stage('Docker Build & Push') {
        steps {
            withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD_', variable: 'DOCKER_HUB_PASSWORD')]) {
                echo "print tag : $LOCAL_TAG"
                sh 'docker login -u thib432 -p $DOCKER_HUB_PASSWORD'
                sh 'docker build -t thib432/java:$LOCAL_TAG .'
                sh 'docker push thib432/java:$LOCAL_TAG'
            }
        }
    }

  environment {
    deploymentName = "devsecops"
    containerName = "devsecops-container"
    serviceName = "devsecops-svc"
    imageName = "thib432/java:${GIT_COMMIT}"
    applicationURL="formationthibaut.eastus.cloudapp.azure.com"
    applicationURI="increment/99"
  }

  stages {


      stage('Build Artifact') {
      steps {
        sh 'mvn clean package -DskipTests=true'
        archive 'target/*.jar' //so that they can be downloaded later test aa
      }
      }

    
    //--------------------------
    stage('UNIT test & jacoco ') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }

    }
//--------------------------
    stage('Mutation Tests - PIT') {
      steps {
        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }
      }
        post { 
         always { 
           pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
         }
       }
    }
//--------------------------

    //--------------------------





    //--------------------------



    
    //--------------------------

       //-------------------------- 


    //--------------------------

    //--------------------------


    

  }
}
}
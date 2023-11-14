pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts artifacts: 'target/*.jar' 
            }
        }  
    
      stage('Unit Tests - Junit and Jacoco') {
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
    stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
     }
         post {
          always {
             pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
              }
            }
   }
         stage('SonarQube - SAST') {
      steps {
         withSonarQubeEnv('SonarQube') {
           sh "mvn sonar:sonar \
	 	              -Dsonar.projectKey=numeric-application \
	 	              -Dsonar.host.url=http://devsecops.clouddevhub.com:9000"
         }
         timeout(time: 2, unit: 'MINUTES') {
           script {
             waitForQualityGate abortPipeline: true
           }
       }
     }
    }

          
 
    stage('Docker Build and Push') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
          sh 'printenv'
          sh 'sudo docker build -t kudidev/numeric-app:""V${BUILD_NUMBER}"" .'-01
          sh 'sudo docker push kudidev/numeric-app:"V$BUILD_NUMBER" '
        }
      }
    }
   stage('K8S Deployment - DEV') {
      steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
              sh "sed -i 's#replace#kudidev/numeric-app:V${BUILD_NUMBER}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
            }
        
      }
    }
}
}

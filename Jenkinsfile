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
 
    stage('Docker Build and Push') {
      steps {
        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
          sh 'printenv'
          sh 'sudo docker build -t kudidev/numeric-app:""V${BUILD_NUMBER}"" .'
          sh 'docker build -t kudidev/numeric-app:latest'
          sh 'docker push kudidev/numeric-app:""V${BUILD_NUMBER}""'
          sh 'docker push kudidev/numeric-app:latest'
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

pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
            }
        }  
    stages {
      stage('Unit Tests') {
            steps {
              sh "mvn clean" 
            }
        }  
    }
}

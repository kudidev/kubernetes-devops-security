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
      stage('mvn test') {
            steps {
              sh "mvn test" 
            }
        }  
    }
}

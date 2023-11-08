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
      stage('unit test') {
            steps {
              sh "mvn test" 
            }
        }  
    }
}

pipeline {
  agent any

  stages {
      stage('Build Artifact - Maven') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
            }
        }
        stage('unit Tests') {
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

    }
}

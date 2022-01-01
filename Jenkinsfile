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
                    sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.host.url=http://devsecops-demo-akash.eastus.cloudapp.azure.com:9000 -Dsonar.login=2a6bed02726494958983f3f9d8e3cae9cb8b1cf2"
                  }
              }
      stage('Docker Build and Push') {
            steps {
              withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
                sh 'printenv'
                sh 'docker build -t agarwa67/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push agarwa67/numeric-app:""$GIT_COMMIT""'
              }
            }
          }

    stage('Kubernetes Deployment - DEV') {
          steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
              sh "sed -i 's#replace#agarwa67/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
            }
          }
        }
    }
}

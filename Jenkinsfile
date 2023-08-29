pipeline {
  agent any 
  environment {
    PATH = "$PATH:/opt/apache-maven-3.9.3/bin"
    VERSION = "${env.BUILD_ID}"
    NEXUS_URL = "65.0.110.219:8081"
    DOCKER_HOSTED = "65.0.110.219:8083"
  }

  stages {
    stage('CLEAN WORKSPACE'){
      steps{
        cleanWs()
      }
    }

    stage('CODE CHECKOUT'){
      steps{
        git 'https://github.com/prajwal1691/devops_real_time_project_1.git'
      }
    }
  }
}  
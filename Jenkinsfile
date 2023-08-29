pipeline {
  agent any 
  environment {
    PATH = "$PATH:/opt/apache-maven-3.9.4/bin"
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

    stage('MODIFIED IMAGE TAG'){
      steps{
        sh '''
                   sed "s/image-name:latest/${DOCKER_HOSTED}/springapp:${VERSION}/g" playbooks/dep_svc.yml
                   sed -i "s/image-name:latest/${DOCKER_HOSTED}/springapp:${VERSION}/g" playbooks/dep_svc.yml
                   sed -i "s/IMAGE_NAME/${DOCKER_HOSTED}/springapp:${VERSION}/g" webapp/src/main/webapp/index.jsp
                   '''
      }
    }

    stage('BUILD'){
      steps{
        sh 'mvn clean install package'
      }
    }

  }
}  
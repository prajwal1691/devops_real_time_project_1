pipeline {
  agent any 
  environment {
    PATH = "$PATH:/opt/apache-maven-3.9.4/bin"
    VERSION = "${env.BUILD_ID}"
    NEXUS_URL = "65.0.110.219:8081"
    DOCKER_HOSTED = "65.0.110.219:8083"
    AWS_DEFAULT_REGION = 'ap-south-1'
    SSH_KEY_PATH = "/var/lib/jenkins/workspace/devops_project/terraform_ansible.pem"
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
                   sed -i 's/DOCKER_HOSTED/${DOCKER_HOSTED}/g' playbooks/dep_svc.yml
                   sed -i 's/DOCKER_HOSTED/${DOCKER_HOSTED}/g' webapp/src/main/webapp/index.jsp
                   sed -i 's/image-name:latest/springapp:${VERSION}/g' playbooks/dep_svc.yml
                   sed -i 's/IMAGE_NAME/springapp:${VERSION}/g' webapp/src/main/webapp/index.jsp
                   '''
      }
    }

    stage('BUILD'){
      steps{
        sh 'mvn clean install package'
      }
    }

    stage("sonar quality check") {
      steps{
        script {
          withSonarQubeEnv(credentialsId: 'sonar-token') {
            sh 'mvn sonar:sonar -Dsonar.projectName=$JOB_NAME \
                                -Dsonar.projectKey=$JOB_NAME \
                                -Dsonar.host.url=http://13.234.238.173:9000 \
                                -Dsonar.token=$sonar-token'
          }

          timeout(time: 1, unit: 'HOURS') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
              error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
          }
        } 
      }
    }
    
    stage('COPY JAR & DOCKERFILE') {
            steps {
              withCredentials([string(credentialsId: 'aws_access_key_id', variable: 'AWS_ACCESS_KEY_ID'), string(credentialsId: 'aws_secret_access_key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
                sh '''
                        aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                        aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                        aws configure set default.region $AWS_DEFAULT_REGION
                        ansible-playbook -e ansible_ssh_private_key_file=$SSH_KEY_PATH playbooks/create_directory.yml
                    '''
              }
            }
        } 
  }
}  
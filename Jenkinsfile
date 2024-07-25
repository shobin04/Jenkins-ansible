pipeline {
    agent any
    
    environment {
            SONAR_RUNNER_HOME = tool 'SonarQube'
            PROJECT_NAME = "ansible"
           }
    tools {
        maven 'maven'
    }
    stages {
        stage ('Petclinic checkout') {
           steps {
               git branch: 'main', url: 'https://github.com/shobin04/Petclinic-app.git'
            }
        }
       stage ('Ansible checkout') {
           steps {
               git branch: 'main', url: 'https://github.com/shobin04/Jenkins-ansible.git'
           }
       }
       stage('Unit Test') {
            steps {
                sh './mvnw clean test'
            }
        }
       stage('SonarQube') {
          steps {
            withSonarQubeEnv('SonarQube') {
                sh '''
                mvn clean verify sonar:sonar \
               -Dsonar.projectKey=ansible \
               -Dsonar.projectName='ansible' \
               -Dsonar.host.url=http://54.227.47.234:9000 \
               -Dsonar.token=sqp_155caf8788135eacef28b1f29f54967c309d6190
              '''
            }
        }
    }
        stage ('build') {
            steps {
               sh""" cd /var/lib/jenkins/workspace/Petclinic-demo/
               mvn package
               """
            }
        }  

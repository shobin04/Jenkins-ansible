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
       stage('SonarQube') {
          steps {
            withSonarQubeEnv('SonarQube') {
                sh ''' cd /var/lib/jenkins/workspace/Petclinic-demo/
                mvn clean verify sonar:sonar \
               -Dsonar.projectKey=ansible \
               -Dsonar.projectName='ansible' \
               -Dsonar.host.url=http://54.227.47.234:9000 \
               -Dsonar.token=sqp_155caf8788135eacef28b1f29f54967c309d6190
              '''
            }
        }
    }
        stage('Quality Gate Check') {
            steps {
                script {
                    def qg = waitForQualityGate()  // Note: This requires the SonarQube Scanner for Jenkins plugin
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
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
        stage ('deploy') {
            steps {
                 sh""" cd /opt/ansible/Jenkins-ansible/
                 ansible-playbook sample_playbook.yaml -i inventory
                 """
            }
        }
    }
}
        

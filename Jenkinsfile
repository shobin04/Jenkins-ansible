pipeline {
    agent any
    
    environment {
            SONAR_RUNNER_HOME = tool 'SonarQube'
            PROJECT_NAME = "ansible-jenkins"
            ANSIBLE_PLAYBOOK = 'ansible/sample_playbook.yaml'
            ANSIBLE_INVENTORY = 'ansible/hosts'
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
                sh 'mvn clean test'  // Use './mvnw' if using the Maven wrapper
            }
        }
       stage('SonarQube') {
          steps {
            withSonarQubeEnv('SonarQube') {
                sh ''' 
                mvn clean verify sonar:sonar \
               -Dsonar.projectKey=ansible-jenkins \
               -Dsonar.projectName='ansible-jenkins' \
               -Dsonar.host.url=http://3.95.216.80:9000 \
               -Dsonar.token=sqp_ee21b6e3a8c4fd568e5272e5b393302ab7321cb2
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
               sh 'mvn package'
            }
        }  
        stage ('deploy') {
            steps {
                 sh 'ansiblePlaybook colorized: true, playbook: "${ANSIBLE_PLAYBOOK}", inventory: "${ANSIBLE_INVENTORY}"'
            }
        }
    }
}
        

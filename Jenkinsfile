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
        // stage('Unit Test') {
        //     steps {
        //         sh """ cd /var/lib/jenkins/workspace/Petclinic-demo/
        //         mvn clean test
        //         """  // Use './mvnw' if using the Maven wrapper
        //     }
        // }
    //    stage('SonarQube') {
    //       steps {
    //         withSonarQubeEnv('SonarQube') {
    //             sh ''' cd /var/lib/jenkins/workspace/Petclinic-demo/
    //             mvn clean verify sonar:sonar \
    //            -Dsonar.projectKey=ansible-jenkins \
    //            -Dsonar.projectName='ansible-jenkins' \
    //            -Dsonar.host.url=http://3.95.216.80:9000 \
    //            -Dsonar.token=sqp_ee21b6e3a8c4fd568e5272e5b393302ab7321cb2
    //           '''
    //         }
    //     }
    // }
    //     stage ('build') {
    //         steps {
    //            sh """ cd /var/lib/jenkins/workspace/Petclinic-demo/
    //             mvn package
    //             """
    //         }
    //     }  
        stage ('deploy using ansible') {
            steps {
                 ansiblePlaybook(
                    colorized: true, 
                    playbook: 'opt/ansible/Jenkins-ansible/sample_playbook.yaml', 
                    inventory: 'opt/ansible/Jenkins-ansible/inventory'
                )
            }
        }
        stage('Security Test') {
            steps {
                sh """ cd /var/lib/jenkins/workspace/Petclinic-demo/
                ./zap.sh -daemon -config api.disablekey=true -newsession -sessionPath session -config globalexcludeurl.url_list.url(0).regex=".*(test-1|test-2).*" \
                zap-cli quick-scan --self-contained http://localhost:8080 \
                zap-cli report -o zap-report.html -f html \
                """
            }
        }
        stage('Performance Testing') {
            steps {
                sh """ cd /var/lib/jenkins/workspace/Petclinic-demo/
                jmeter -n -t petclinic_test_plan.jmx -l results.jtl \
                sh 'jmeter -g results.jtl -o jmeter-report \
                """
            }
        }
    }
}
        

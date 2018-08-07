pipeline {
    agent any

    environment {
      PATH = "$PATH:/usr/local/bin"
      tf_url = "https://releases.hashicorp.com/terraform/0.11.7/terraform_0.11.7_linux_amd64.zip"
      tf_package = "terraform_0.11.7_linux_amd64.zip"
    }
    
    stages {
        stage('stage terraform') {
            steps {
                sh 'if [ -d tf_scripts ] ; then sudo rm -rf tf_scripts ; fi && git clone https://github.com/rbremer84/terraform.git tf_scripts/'
                sh 'aws s3 cp s3://rbremer-temp/terraform.tfvars tf_scripts/'
                sh 'wget -P tf_scripts/ $tf_url && unzip -o tf_scripts/$tf_package -d tf_scripts/ ; rm -f tf_scripts/$tf_package'
                sh 'cd tf_scripts/ && ./terraform init'
            }
        }
        stage('stage ansible') {
            steps {
                sh 'if [ -d playbooks ] ; then sudo rm -rf playbooks ; fi && git clone https://github.com/rbremer84/Ansible.git playbooks/'
            }
        } 
        stage('tf plan') {
            steps {               
                sh 'ansible-playbook -i playbooks/hosts playbooks/plan_ec2.yml'
            }
        }
        stage('tf create') {
            steps {               
                sh 'ansible-playbook -i playbooks/hosts playbooks/create_ec2.yml'
            }
        }
        stage('tf destroy') {
            steps {               
                sh 'ansible-playbook -i playbooks/hosts playbooks/destroy_ec2.yml'
            }
        }        
    }
}

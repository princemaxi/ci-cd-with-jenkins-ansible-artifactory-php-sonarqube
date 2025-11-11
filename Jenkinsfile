pipeline {
    agent any

    parameters {
        string(name: 'inventory', defaultValue: 'dev', description: 'Inventory to run against (dev, sit, pentest, ci)')
        string(name: 'ansible_tags', defaultValue: 'all', description: 'Ansible tags to run (e.g., all, provision, deploy-todo)')
    }

    environment {
        // Points to the ansible.cfg file in our repository
        ANSIBLE_CONFIG = "${WORKSPACE}/ansible/ansible.cfg"
    }

    stages {
        stage('Initial cleanup') {
            steps {
                // Clean workspace before checkout, as recommended
                deleteDir()
            }
        }

        stage('Checkout SCM') {
            steps {
                // Checkout this (ansible-config-mgt) repository
                git branch: 'main', url: 'https://github.com/princemaxi/ansible-config-mgt.git'
            }
        }

        stage('Prepare Ansible Config') {
            steps {
                script {
                    // This dynamically updates the roles_path in ansible.cfg
                    // to point to the correct path inside the Jenkins workspace,
                    // as recommended in "Possible Issues to Watch out for".
                    sh "sed -i 's|roles_path = .*|roles_path = ${WORKSPACE}/ansible/roles|' ${ANSIBLE_CONFIG}"
                }
            }
        }

        stage('Run Ansible') {
            steps {
                script {
                    // Run the main site.yml playbook, using the parameters
                    // to select the correct inventory and tags.
                    sh "ansible-playbook -i ansible/inventories/${params.inventory}/hosts.ini ansible/playbooks/site.yml --tags ${params.ansible_tags}"
                }
            }
        }
    }
}
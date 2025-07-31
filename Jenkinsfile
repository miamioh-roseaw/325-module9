pipeline {
    agent any

    environment {
        ANSIBLE_INVENTORY = 'inventory.ini'
        PLAYBOOK_FILE = 'install_chrome.yml'
        VAULT_PASS = credentials('ansible-vault-pass') // Jenkins secret text
    }

    stages {
        stage('Prepare Environment') {
            steps {
                sh '''
                    echo "[INFO] Setting up Ansible and vault pass file..."

                    echo "${VAULT_PASS}" > vault_pass.txt
                    chmod 600 vault_pass.txt

                    ansible --version
                    ansible-playbook --version
                '''
            }
        }

        stage('Run Chrome Installation Playbook') {
            steps {
                sh '''
                    echo "[INFO] Running Ansible playbook..."
                    ansible-playbook ${PLAYBOOK_FILE} -i ${ANSIBLE_INVENTORY} \
                      --vault-password-file vault_pass.txt
                '''
            }
        }
    }

    post {
        always {
            node {
                sh '''
                    echo "[INFO] Cleaning up vault file..."
                    rm -f vault_pass.txt
                '''
            }
        }

        success {
            echo '[✅] Chrome installed successfully on all hosts.'
        }

        failure {
            echo '[❌] Ansible playbook execution failed.'
        }
    }
}

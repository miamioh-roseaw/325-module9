pipeline {
    agent any

    environment {
        ANSIBLE_INVENTORY = 'inventory.ini'
        PLAYBOOK_FILE = 'install_chrome.yml'
        VAULT_PASS = credentials('ansible-vault-pass') // Jenkins Secret Text credential
    }

    stages {
        stage('Prepare Environment') {
            steps {
                sh '''
                    echo "[INFO] Creating Ansible vault password file..."
                    echo "${VAULT_PASS}" > vault_pass.txt
                    chmod 600 vault_pass.txt
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
            echo '[INFO] Cleaning up vault file...'
            // sh 'rm -f vault_pass.txt'
        }
        success {
            echo '[✅] Chrome installed successfully on all hosts.'
        }
        failure {
            echo '[❌] Ansible playbook execution failed.'
        }
    }
}

pipeline {
    agent any

    environment {
        SCRIPT = 'install_chrome.yml'
        INVENTORY = 'inventory.ini'
        VAULT_PASS = credentials('ansible-vault-pass') // Jenkins secret text with vault password
    }

    stages {
        stage('Setup Ansible Environment') {
            steps {
                sh '''
                    echo "[INFO] Cleaning existing Ansible installations (if any)..."
                    pip3 uninstall -y ansible ansible-core || true
                    rm -rf ~/.local/lib/python3.10/site-packages/ansible*

                    echo "[INFO] Installing Ansible..."
                    pip3 install --user ansible==2.14

                    echo "[INFO] Verifying Ansible version..."
                    ~/.local/bin/ansible-playbook --version
                '''
            }
        }

        stage('Create Vault Password File') {
            steps {
                sh '''
                    echo "[INFO] Writing vault password to vault_pass.txt..."
                    echo "${VAULT_PASS}" > vault_pass.txt
                    chmod 600 vault_pass.txt
                '''
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh '''
                    echo "[INFO] Running Ansible playbook..."
                    ~/.local/bin/ansible-playbook ${SCRIPT} -i ${INVENTORY} --vault-password-file vault_pass.txt
                '''
            }
        }
    }

    post {
        always {
            sh 'rm -f vault_pass.txt'
            echo "[INFO] Cleanup complete."
        }
        failure {
            echo "[ERROR] Jenkins pipeline failed."
        }
        success {
            echo "[SUCCESS] Ansible playbook ran successfully."
        }
    }
}

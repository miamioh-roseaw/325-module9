pipeline {
    agent any

    environment {
        INVENTORY = 'inventory.ini'
        PLAYBOOK = 'install_chrome.yml'
        VAULT_FILE = 'vault_vars.yml'
    }

    stages {
        stage('Install Ansible if Needed') {
            steps {
                sh '''
                    if ! command -v ansible > /dev/null; then
                        echo "[INFO] Installing Ansible..."
                        sudo apt-get update && sudo apt-get install -y ansible
                    fi
                '''
            }
        }

        stage('Install Chrome via Ansible Playbook') {
            steps {
                withCredentials([string(credentialsId: 'ansible-vault-password', variable: 'VAULT_PASS')]) {
                    sh '''
                        echo "$VAULT_PASS" > vault_vars.yml
                        chmod 600 vault_vars.yml

                        echo "[INFO] Running Ansible playbook..."
                        ansible-playbook $PLAYBOOK -i $INVENTORY --vault-password-file vault_vars.yml
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'rm -f vault_vars.yml'
        }
        success {
            echo "✅ Chrome installation completed successfully."
        }
        failure {
            echo "❌ Chrome installation failed."
        }
    }
}

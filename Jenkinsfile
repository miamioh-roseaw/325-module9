pipeline {
  agent any

  environment {
    INVENTORY = 'hosts'
    PLAYBOOK = 'playbook.yaml'
    ANSIBLE_CONFIG = 'ansible.cfg'
    VAULT_VARS = 'vault_vars.yaml'
  }

  stages {
    stage('Install Ansible and Vault Tools') {
      steps {
        sh '''
          if ! command -v pip3 > /dev/null; then
            echo "[INFO] pip3 not found. Installing..."
            wget https://bootstrap.pypa.io/get-pip.py -O get-pip.py
            python3 get-pip.py --user
          fi

          if ! command -v ansible-playbook > /dev/null; then
            echo "[INFO] Installing Ansible..."
            pip3 install --user ansible
          fi
        '''
      }
    }

    stage('Run Ansible Playbook with Vault') {
      environment {
        VAULT_PASS = credentials('ansible-vault-password')
      }
      steps {
        sh '''
          echo "[INFO] Running playbook with Ansible Vault decryption..."
          
          # Save password to file (temporary use only)
          echo "$VAULT_PASS" > .vault_pass.txt

          ansible-playbook $PLAYBOOK -i $INVENTORY \
            --vault-password-file .vault_pass.txt \
            -e @$VAULT_VARS

          # Clean up
          rm -f .vault_pass.txt
        '''
      }
    }
  }

  post {
    always {
      echo '[INFO] Pipeline completed.'
    }
  }
}

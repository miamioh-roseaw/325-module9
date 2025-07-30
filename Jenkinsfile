pipeline {
  agent any

  environment {
    VAULT_PASSWORD_FILE = '.vault_pass.txt'
    ANSIBLE_SSH_EXECUTABLE = 'ssh'
  }

  stages {

    stage('Checkout') {
      steps {
        echo '[INFO] Checking out repository...'
        checkout scm
      }
    }

    stage('Install Ansible') {
      steps {
        sh '''
          echo "[INFO] Installing pip3 if not present..."
          if ! command -v pip3 > /dev/null; then
              wget https://bootstrap.pypa.io/get-pip.py -O get-pip.py
              python3 get-pip.py --user
          fi

          echo "[INFO] Uninstalling old Ansible..."
          pip3 uninstall -y ansible ansible-core || true

          echo "[INFO] Installing Ansible 2.10.7..."
          pip3 install --user ansible==2.10.7
        '''
      }
    }

    stage('Run Ansible Playbook with Vault') {
      steps {
        echo '[INFO] Running Ansible Playbook with Ansible Vault decryption...'
        sh '''
          export PATH=$HOME/.local/bin:$PATH
          ansible-playbook playbook.yaml \
            -i hosts \
            --vault-password-file ${VAULT_PASSWORD_FILE} \
            -e @vault_vars.yaml
        '''
      }
    }
  }
}

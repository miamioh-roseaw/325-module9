pipeline {
  agent any

  environment {
    // Vault secret stored securely in Jenkins credentials
    VAULT_PASSWORD = credentials('ansible-vault-password')
  }

  stages {
    stage('Setup Python Virtualenv') {
      steps {
        sh '''
          echo "[INFO] Installing virtualenv if not already present..."
          pip install --user virtualenv || true

          echo "[INFO] Creating virtualenv for Ansible..."
          python3 -m virtualenv ansible-env

          echo "[INFO] Activating virtualenv and installing Ansible..."
          source ansible-env/bin/activate && \
            pip install ansible==6.7.0
        '''
      }
    }

    stage('Run Chrome Installation Playbook') {
      steps {
        sh '''
          echo "${VAULT_PASSWORD}" > vault_pass.txt
          chmod 600 vault_pass.txt

          echo "[INFO] Running Ansible playbook from virtualenv..."
          source ansible-env/bin/activate && \
            ansible-playbook install_chrome.yml -i inventory.ini --vault-password-file vault_pass.txt
        '''
      }
    }
  }

  post {
    always {
      echo '[INFO] Cleaning up vault file...'
      sh 'rm -f vault_pass.txt'
    }
  }
}

pipeline {
  agent any

  environment {
    VAULT_PASSWORD = credentials('ansible-vault-password')
  }

  stages {
    stage('Precheck System Dependencies') {
      steps {
        sh '''
          echo "[INFO] Verifying Python and pip..."
          which python3 || sudo apt install -y python3
          which pip || sudo apt install -y python3-pip
        '''
      }
    }

 stages {
    stage('Diagnostics') {
      steps {
        sh '''
          echo "[INFO] Checking workspace contents..."
          ls -lah
          echo "[INFO] Showing inventory file..."
          cat inventory.ini
        '''
      }
    }
   
    stage('Setup Python Virtualenv') {
      steps {
        sh '''
          echo "[INFO] Installing virtualenv if not already present..."
          pip install --user virtualenv || true

          echo "[INFO] Creating virtualenv for Ansible..."
          python3 -m virtualenv ansible-env

          echo "[INFO] Activating virtualenv and installing Ansible and collections..."
          . ansible-env/bin/activate && \
            pip install ansible==6.7.0 && \
            ansible-galaxy collection install ansible.windows community.general
        '''
      }
    }

    stage('Run Chrome Installation Playbook') {
      steps {
        sh '''
          echo "${VAULT_PASSWORD}" > vault_pass.txt
          chmod 600 vault_pass.txt

          echo "[INFO] Running Ansible playbook..."
          . ansible-env/bin/activate && \
            ansible-playbook install_chrome.yml -i inventory.ini --vault-password-file vault_pass.txt -vvv
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

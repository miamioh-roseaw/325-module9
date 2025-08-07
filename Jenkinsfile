pipeline {
  agent any

  environment {
    ANSIBLE_VAULT_PASS = credentials('ansible-vault-password') // Jenkins Secret Text
  }

  stages {
    
    stage('Setup Environment') {
      steps {
        sh '''
          set -e
          sudo apt-get update
          sudo apt-get install -y python3.10-venv python3-pip
          python3 -m venv ansible-env
          . ansible-env/bin/activate && \
          pip install --upgrade pip && \
          pip install ansible pywinrm && \
          ansible-galaxy collection install ansible.windows community.windows
        '''
      }
    }

    stage('Write Vault Password') {
      steps {
        writeFile file: 'vault_pass.txt', text: "${ANSIBLE_VAULT_PASS}"
      }
    }
    
    stage('Run Playbook') {
      steps {
        sh '''
          set -e
          . ansible-env/bin/activate && \
          ansible-playbook install_cross_platform.yml \
            -i inventory.ini \
            --vault-password-file vault_pass.txt
        '''
      }
    }

    stage('Cleanup Vault File') {
      steps {
        sh 'shred -u vault_pass.txt || rm -f vault_pass.txt'
      }
    }
  }

  post {
    always {
      sh 'rm -rf ansible-env'
    }
  }
}

pipeline {
  agent any

  environment {
    ANSIBLE_VAULT_PASS = credentials('ansible-vault-password') // Jenkins secret text credential
  }

  stages {
    stage('Setup Python Virtualenv') {
      steps {
        sh '''
          python3 -m venv ansible-env
          . ansible-env/bin/activate && \
            pip install --upgrade pip && \
            pip install ansible pywinrm && \
            ansible-galaxy collection install ansible.windows community.windows
        '''
      }
    }

    stage('Write Vault Password File') {
      steps {
        writeFile file: 'vault_pass.txt', text: "${ANSIBLE_VAULT_PASS}"
      }
    }

    stage('Run Ansible Playbook') {
      steps {
        sh '''
          . ansible-env/bin/activate && \
          ansible-playbook install_cross_platform.yml \
            -i inventory.ini \
            --vault-password-file vault_pass.txt
        '''
      }
    }

    stage('Cleanup Vault Password') {
      steps {
        sh 'rm -f vault_pass.txt'
      }
    }
  }

  post {
    always {
      sh 'rm -rf ansible-env vault_pass.txt'
    }
  }
}

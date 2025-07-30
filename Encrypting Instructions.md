1. Create the vault_vars.yaml file
Create a file named vault_vars.yaml with your Ansible variables like this:

  ansible_user: student

  ansible_password: Passw0rd!

2. Encrypt the file using Ansible Vault (you will need to install ansible-core in terminal on your jenkins-server using 'sudo apt install ansible-core'
On the command line, run:

  ansible-vault encrypt vault_vars.yaml
  
It will ask you to enter a Vault password. This is the password Jenkins will use later to decrypt the file.

3. Store the Vault password in Jenkins
Open Jenkins in your browser.

Go to Manage Jenkins > Manage Credentials.

Select your domain (like “(global)”).

Click Add Credentials.

Choose:

  Kind: Secret text

  Secret: the Vault password you used in step 2

  ID: ansible-vault-password

  Description: something like "Ansible Vault Password"

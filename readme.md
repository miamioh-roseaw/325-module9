#1) Set up ssh on linux workstations:
    sudo apt update
    sudo apt install openssh-server
    
#2) set up winrm on Windows workstations (Run-As Administator on Windows PowerShell):
    winrm quickconfig -force
    Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
    Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
    Enable-NetFirewallRule -Name "WINRM-HTTP-In-TCP"
    
#3) Create the vault_vars.yaml file Create a file named vault_vars.yaml with your Ansible variables like this:
    ansible_user: student
    ansible_password: Passw0rd!

#4)Encrypt the file using Ansible Vault (you will need to install ansible-core in terminal on your jenkins-server using 'sudo apt install ansible-core' On the command line, run:
    ansible-vault encrypt vault_vars.yaml

#5) It will ask you to enter a Vault password. This is the password Jenkins will use later to decrypt the file.  Store the Vault password in Jenkins. Open Jenkins in your browser.
Go to Manage Jenkins > Manage Credentials.  Select your domain (like “(global)”). Click Add Credentials.
Choose:
    Kind: Secret text
    Secret: the Vault password you used in step 2
    ID: ansible-vault-password
    Description: something like "Ansible Vault Password"

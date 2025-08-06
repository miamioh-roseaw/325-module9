    #Setting up ssh on linux workstations:
    sudo apt update
    sudo apt install openssh-server
    
    #setting up winrm on Windows workstations:
    winrm quickconfig -force
    Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
    Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
    Enable-NetFirewallRule -Name "WINRM-HTTP-In-TCP"

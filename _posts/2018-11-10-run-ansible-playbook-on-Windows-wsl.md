---
layout: post
title:  "Step by Step - Run ANSIBLE Azure Playbook on WSL"
categories: ANSIBLE
tags: Azure Cloud ANSIBLE InfraAsCode 
author: dovives
---

* content
{:toc}

# Description 

I thought it might be useful for others to run ANSIBLE playbook for Azure on Windows thanks to WSL 




# Prerequisites for WSL on Windows   

## Windows version 

First you should check you Windows build is *16215 or later*.

You can run the  `winver` command to check you current build OS (in CMD, Start, Run) : 

![Windows Version with winver](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/WinVer.png.jpg)


## WSL Installation on Windows    

First make sure WSL feature is activated on your Windows machine. 

Start PowerShell as administrator and run the following command :

- `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`

Then, download Ubuntu distros using the following command : 

- `curl.exe -L -o ubuntu-1604.appx https://aka.ms/wsl-ubuntu-1604`

If you've already installed WSL, I recommand using the latest distros version otherwise to update : `sudo do-release-upgrade`

To check your current Distros version run `screenfetch` in WSL. 




# ANSIBLE Installation on WSL 

## Install ANSIBLE package 

To ANSIBLE run the following command to install the ANSIBLE package :

```
sudo apt-get update
sudo apt-get install python-pip git libffi-dev libssl-dev -y
pip install ansible pywinrm
sudo apt-add-repository ppa:ansible/ansible
```

![ANSIBLE Installation On WSL](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/ANSIBLE-Installation-WSL.jpg)

## Check ANSIBLE version

You can run the following command to check your ANSIBLE version - this also ensure installation ran correctly: 

- `ansible --version`


## Add your ANSIBLE managed host 

Add your ansible managed hosts into the `hosts` file (use `vi`) :

![ANSIBLE hosts](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/ANSIBLE_Host.jpg)

If you use a Windows Machine, make sure to have configure your Windows Host correctly : 

- [WinRM Setup for ANSIBLE](https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html)

## Test connectivity and authentication 

To confirm ANSIBLE execute correctly, we will simply execute a ansible ping command (linux example below): 

- ```ansible Linux -m ping --private-key "EC2_Credentials.pem" -u ec2-user```

![ANSIBLE Ping Linux](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/ANSIBLE_Ping_LinuxVM.jpg)

You can check with Windows host with : 

- ```ansible Windows -i ./hosts -m win_ping -u user@domain.com```

![ANSIBLE Ping Windows](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/ANSIBLE_Ping_WindowsVM.jpg)




# Run Azure Playbook 

## Install Azure Module for ANSIBLE

First install Azure Module : 

- ```pip install ansible[azure]```

## Downlaod Azure samples from Github

Azure team already provide a list of playbook that yon can play with OOB: 
[](https://github.com/Azure-Samples/ansible-playbooks)


I recommend downloading the samples on you **Windows Machine** :

- ```git clone https://github.com/Azure-Samples/ansible-playbooks.git```

When available on your local machine, you can access them from WSL from :

- ```/mnt/<drive>/<windowspath>```

## Add Azure Credentials or Login on Azure with Az CLI

They are three options to authenticate on Azure in order to run ANSIBLE PLaybook on Azure. 

- First option, set the following environment variables:

```ini
AZURE_CLIENT_ID=<service_principal_client_id>
AZURE_SECRET=<service_principal_password>
AZURE_SUBSCRIPTION_ID=<azure_subscription_id>
AZURE_TENANT=<azure_tenant_id>
```

- Second option, add the following content to the file `$HOME/.azure/credentials`:

```ini
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
secret=xxxxxxxxxxxxxxxxx
tenant=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

    To create Client ID & Client Secret App in your Azure Subscription, you can rely on the following article: 
[](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)


- Third option, do a az login:

```sh
az login
```

To install Azure CLI, you can rely on the following article: 
[](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest)


## Run a sample playbook from WSL 

In this example I login first with `az login`. 

Then I created the Resource Group through with Az ClI. run the following  

```bash
az group create --name ansible_test_rg --location "West europe"

{
  "id": "/subscriptions/1619bfac-1484-4da0-95cc-dec25338e962/resourceGroups/ansible_test_rg",
  "location": "westeurope",
  "managedBy": null,
  "name": "ansible_test_rg",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null

```

Then, I run the following command to run the play book :

- `ansible-playbook vm_create_windows.yml -e "resource_group_name=ansible_test_rg"`

![ANSIBLE Run PLaybook](https://raw.githubusercontent.com/Dovives/dovives.github.io/master/images/20181110-Ansible-on-WSL/ANSIBLE_Run_PlayBook.jpg)

Note: Make sure to be in the correct directory or specify the whole path to the PlayBook. 

## Resources

[Ansible on Azure](https://docs.microsoft.com/en-us/azure/ansible/ansible-overview)

[Get Started with Azure](http://docs.ansible.com/ansible/latest/guide_azure.html)

[Ansible Playbook](http://docs.ansible.com/ansible/latest/playbooks.html)

[Ansible role azure_preview_modules](https://galaxy.ansible.com/Azure/azure_preview_modules/)

[Ansible Galaxy](http://galaxy.ansible.com) for example roles from the Ansible community for deploying many popular applications. 





### Hope this Help! 

### Dominique V. 
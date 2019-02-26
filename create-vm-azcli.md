# Create VM with Azure CLI, using Cloud Shell

## Introduction
The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This tutorial shows you how to use the Azure CLI to deploy a Linux virtual machine with Ubuntu 16.04 LTS. Like in the previous tutorial, you will also connect to it using SSH and install the NGINX web server.

If you don't have an Azure subscription, create a free account before you begin.

## Launch Azure Cloud Shell
The Azure Cloud Shell is a free interactive shell that you can use to run the steps in this article. It has common Azure tools preinstalled and configured to use with your account.

To open the Cloud Shell, just select Try it from the upper right corner of a code block. 
<p align="left">
  <img width="50%" hspace="20" height="50%" src="./media/cloudshell-arrow.png">
</p>
<br>




You can also launch Cloud Shell in a separate browser tab by going to https://shell.azure.com/bash. Select Copy to copy the blocks of code, paste it into the Cloud Shell, and press enter to run it.

If you prefer to install and use the CLI locally, this quickstart requires Azure CLI version 2.0.30 or later. Run az --version to find the version. If you need to install or upgrade, see Install Azure CLI.

Create a resource group
Create a resource group with the az group create command. An Azure resource group is a logical container into which Azure resources are deployed and managed. The following example creates a resource group named myResourceGroup in the eastus location:

Azure CLI

Copy

Try It
az group create --name myResourceGroup --location eastus
Create virtual machine
Create a VM with the az vm create command.

The following example creates a VM named myVM and adds a user account named azureuser. The --generate-ssh-keys parameter is used to automatically generate an SSH key, and put it in the default key location (~/.ssh). To use a specific set of keys instead, use the --ssh-key-value option.

Azure CLI

Copy

Try It
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
It takes a few minutes to create the VM and supporting resources. The following example output shows the VM create operation was successful.


Copy
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
Note your own publicIpAddress in the output from your VM. This address is used to access the VM in the next steps.

Open port 80 for web traffic
By default, only SSH connections are opened when you create a Linux VM in Azure. Use az vm open-port to open TCP port 80 for use with the NGINX web server:

Azure CLI

Copy

Try It
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
Connect to virtual machine
SSH to your VM as normal. Replace publicIpAddress with the public IP address of your VM as noted in the previous output from your VM:

bash

Copy
ssh azureuser@publicIpAddress
Install web server
To see your VM in action, install the NGINX web server. Update your package sources and then install the latest NGINX package.

bash

Copy
sudo apt-get -y update
sudo apt-get -y install nginx
When done, type exit to leave the SSH session.

View the web server in action
Use a web browser of your choice to view the default NGINX welcome page. Use the public IP address of your VM as the web address. The following example shows the default NGINX web site:
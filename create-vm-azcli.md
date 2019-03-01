# Create VM with Azure CLI, using Cloud Shell

## Introduction
The Azure CLI is used to create and manage Azure resources from the command line or in scripts. This tutorial shows you how to use the Azure CLI to deploy a Linux virtual machine with Ubuntu 16.04 LTS. Like in the previous tutorial, you will also connect to it using SSH and install the NGINX web server.

If you don't have an Azure subscription, create a free account before you begin.

## Launch Azure Cloud Shell
The Azure Cloud Shell is a free interactive shell that you can use to run the steps in this article. You can of course use a shell on your local computer as well, but since the Azure Cloud Shell comes pre installed with all needed Azure tools and is configured to be used from your account, it is a convenient choice.

To open the Cloud Shell, just select Try it from the upper right corner of a code block. This will open up a shell in your browser. Select **bash** as the type of shell to use.
<p align="left">
  <img width="50%" height="50%" src="./media/cloudshell-arrow.png">
</p>
<br>

You can also launch Cloud Shell in a separate browser tab by going to https://shell.azure.com/bash. 

## Resource Group
An Azure resource group is a logical container into which Azure resources are deployed and managed. For this workshop, a resource group has already been created, that everyone will use. The resource group name is **VG-A-33858-LAB-RG**

## Create virtual machine
A Virtual Machine can be created with the ````az vm create```` command. Along with the create command you will pass a number of information elements, sfor instance which **resource group** to use, which **operating system** and **VM size**. For VM size you will use **A0** which is one currently the cheapest VM that can be used.

### Note: since we all share the same resource group, each VM needs a unique name. To make sure that is the case, you could for instance use your corporate signum in the name of the VM.

The following example creates a VM named **pelithnevm** and adds a user account named azureuser. 

The admin-username and admin-password will be used to login to the VM. Note that it is not a recommended security practice to enter a password in clear text as below. Instead ssh-keys should be used. In the next tutorial step, this will be the case.

```console
az vm create \
  --resource-group VG-A-33858-LAB-RG \
  --name pelithnevm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password A-very-secure-passw0rd \
  --size Basic_A0
  ```
  
It takes a few minutes to create the VM and supporting resources. The following example output shows the VM create operation was successful.
````console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/pelithnevm",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "VG-A-33858-LAB-RG"
}
````

Note your own publicIpAddress in the output from your VM. This address is used to access the VM in the next steps.

## Open port 80 for web traffic
By default, only SSH connections are opened when you create a Linux VM in Azure. Use az vm open-port to also open TCP port 80 for use with the NGINX web server:

````console
az vm open-port --port 80 --resource-group VG-A-33858-LAB-RG --name pelithnevm
````

## Connect to virtual machine
SSH to your VM as normal. Replace publicIpAddress with the public IP address of your VM as noted in the previous output from your VM:

````console
ssh azureuser@publicIpAddress
````


## Install web server
To see your VM in action, install the NGINX web server. Update your package sources and then install the latest NGINX package.

````console
sudo apt-get -y update
sudo apt-get -y install nginx
````

When done, type exit to leave the SSH session.

## View the web server in action
Use a web browser of your choice to view the default NGINX welcome page. Use the public IP address of your VM as the web address. The following example shows the default NGINX web site:
<p align="left">
  <img width="75%" height="75%" hspace="20" src="./media/nginx.png">
</p>

## Next step
Next step is to create another VM, but with more customized configuration, using the Azure CLI. Click <a href="https://github.com/pelithne/azure-workshop/blob/master/custom-config.md">here</a> to continue to this step.

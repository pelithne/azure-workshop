# Load balance Linux virtual machines in Azure to create a highly available application

## Introduction
Load balancing provides a higher level of availability by spreading incoming requests across multiple virtual machines. In this tutorial, you learn about the different components of the Azure load balancer that distribute traffic and provide high availability. 

## Azure load balancer overview
An Azure load balancer is a Layer-4 (TCP, UDP) load balancer that provides high availability by distributing incoming traffic among a set of VMs.

You define a front-end IP configuration that contains one or more public IP addresses. This front-end IP configuration allows your load balancer and applications to be accessible over the Internet.

Virtual machines connect to a load balancer using their virtual network interface card (NIC). To distribute traffic to the VMs, a back-end address pool contains the IP addresses of the virtual (NICs) connected to the load balancer.

To control the flow of traffic, you define load balancer rules for specific ports and protocols that map to your VMs.

### Create a public IP address
To access your app on the Internet, you need a public IP address for the load balancer. Create a public IP address with ````az network public-ip create````. The following example creates a public IP address named pelithneIP in the VG-A-33858-LAB-RG resource group. 

### note: give the public ip a unique name, e.g. by using your corporate signum. This is important, since you are all working in the same Resource Group.

````console
az network public-ip create \
    --resource-group VG-A-33858-LAB-RG \
    --name pelithneIP
 ````
 
### Create a load balancer
Create a load balancer with ````az network lb create````. The following example creates a load balancer named pelithneLB and assigns the newly created IP address to the front-end IP configuration.

### note: give the Load Balancer a unique name, e.g. by using your corporate signum.

````console
az network lb create \
    --resource-group VG-A-33858-LAB-RG \
    --name pelithneLB \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address pelithneIP
````

### Create a load balancer rule
A load balancer rule is used to define how traffic is distributed to the VMs. You define the front-end IP configuration for the incoming traffic and the back-end IP pool to receive the traffic, along with the required source and destination port. 

Create a load balancer rule with ````az network lb rule create````. The following example creates a rule named pelithneLBRule, uses the myHealthProbe health probe, and balances traffic on port 80:

### note: give the LB Rule a unique name, e.g. by using your corporate signum.

````console
az network lb rule create \
    --resource-group VG-A-33858-LAB-RG \
    --lb-name pelithneLB \
    --name pelithneLBRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
````

## Configure virtual network
Before you deploy some VMs and can test your balancer, create the supporting virtual network resources. 

### Create network resources
Create a virtual network with ````az network vnet create````. The following example creates a virtual network named pelithneVnet with a subnet named mySubnet:

### note: give the pvnet a unique name, e.g. by using your corporate signum. The subnet can have a generic name though, since it resides under your (already unique) vnet.

````console
az network vnet create \
    --resource-group VG-A-33858-LAB-RG \
    --name pelithneVnet \
    --subnet-name mySubnet
````

To add a network security group, you use ````az network nsg create````. The following example creates a network security group named pelithneNSG.

### note: give the nsg a unique name, e.g. by using your corporate signum. 

az network nsg create \
    --resource-group VG-A-33858-LAB-RG \
    --name pelithneNSG

Create a network security group rule with ````az network nsg rule create````. The following example creates a network security group rule named myNetworkSecurityGroupRule:

````console
az network nsg rule create \
    --resource-group VG-A-33858-LAB-RG \
    --nsg-name pelithneNSG \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
````

Virtual NICs are created with az network nic create. The following example creates three virtual NICs. (One virtual NIC for each VM you create for your app in the following steps). You can create additional virtual NICs and VMs at any time and add them to the load balancer:

````console
for i in `seq 1 3`; do
    az network nic create \
        --resource-group VG-A-33858-LAB-RG \
        --name pelithneNic$i \
        --vnet-name pelithneVnet \
        --subnet mySubnet \
        --network-security-group pelithneNSG \
        --lb-name pelithneLB \
        --lb-address-pools myBackEndPool
done
````



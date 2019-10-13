---
lab:
    title: 'Configure Azure DNS'
    module: 'Module 04 - Virtual Networking'
---

# Lab: Configure Azure DNS
  
All tasks in this lab are performed from the Azure portal


### Scenario
  
Adatum Corporation wants to implement public and private DNS service in Azure without having to deploy its own DNS servers. 


### Objectives
  
After completing this lab, you will be able to:

- Configure Azure DNS for public domains

- Configure Azure DNS for private domains


### Exercise 1: Configure Azure DNS for public domains

The main tasks for this exercise are as follows:

1. Create a public DNS zone

1. Create a DNS record in the public DNS zone

1. Validate Azure DNS-based name resolution for the public domain


#### Task 1: Create a public DNS zone
  
1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **DNS zone**.

1. Select **DNS Zone**, and then click **Create**.

1. From the to **Create DNS zone** blade, create a new DNS zone with the following settings: 

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: the name of a new resource group **az1000401b-RG**

    - Name: any unique, valid DNS domain name in the **.com** namespace

    - Resource group location: **East US** (or a supported region near you)


#### Task 2: Create a DNS record in the public DNS zone
  
1. From your lab computer open a Powershell session, run the following in order to identify the public IP address of your lab computer:

   ```pwsh
   Invoke-RestMethod http://ipinfo.io/json | Select-Object -ExpandProperty IP
   ```

   > **Note**: Take a note of this IP address. You will use it later in this task.

1. From the Azure Portal, navigate to **Public IP Addresses** services and create a new Public IP address

    - IP Version: **IPv4**

    - SKU: **Basic**

    - Name: **az1000401b-pip**

    - IP address assignment: **Static**

    - Resource Group: **az1000401b-RG**

    - Location: **East US**

1. In the Azure portal, navigate to the **az1000401b-RG** resource group blade.

1. From the **az1000401b-RG** resource group blade, navigate to the blade displaying newly created public DNS zone.

1. From the DNS zone blade, click **+ Record set** to navigate to the **Add record set** blade

1. Create a DNS record with the following settings:

    - Name: **mylabvmpip**

    - Type: **A**

    - Alias record set: **No**

    - TTL: **1**

    - TTL unit: **Hours**

    - IP ADDRESS: the public IP address of your lab computer you identified earlier in this task

1. From the Overview blade, click **+ Record set**, and create another record with the following settings:

    - Name: **myazurepip**

    - Type: **A**

    - Alias record set: **Yes**

    - Alias type: **Azure resource**

    - Choose a subscription: the name of the Azure subscription you are using in this lab

    - Azure resource: **az1000401b-pip**

    - TTL: **1**

    - TTL unit: **Hours**


#### Task 3: Validate Azure DNS-based name resolution for the public domain

1. On the DNS zone blade, note the list of the name servers that host the zone you created. You will use the first of them named in the next step.

1. From the lab virtual machine, start Command Prompt and run the following to validate the name resolution of the two newly created DNS records (where &lt;custom_DNS_domain&gt; represents the custom DNS domain you created in the first task of this exercise and &lt;name_server&gt; represents the name of the DNS name server you identified in the previous step): 

   ```
   nslookup mylabvmpip.<custom_DNS_domain> <name_server>
   
   nslookup myazurepip.<custom_DNS_domain> <name_server>
   ```

1. Verify that the IP addresses returned match those you identified earlier in this task.

> **Result**: After you completed this exercise, you have created a public DNS zone, created a DNS record in the public DNS zone, and validated Azure DNS-based name resolution for the public domain.


### Exercise 2: Configure Azure DNS for private domains
  
The main tasks for this exercise are as follows:

1. Provision a multi-virtual network environment

1. Create a private DNS zone

1. Deploy Azure VMs into virtual networks

1. Validate Azure DNS-based name reservation and resolution for the private domain


#### Task 1: Provision a multi-virtual network environment
  
1. From the Azure Portal, create new resource group as below:

   - Resource group location: East US (or a supported region near you)
   - Name: az1000402b-RG

1. Create Virtual Network with below IP address configuration in same region:

    - Vnet 1: 10.104.0.0/16
    - Vnet 1 name: az1000402b-vnet1
    - Subnet name: subnet1
    - Subnet: 10.104.0.0/24
    - Resource Group Name: az1000402b-RG

1. Create second Virtual Network with below IP address configuration in same region:

    - Vnet 2: 10.204.0.0/16
    - Vnet 1 name: az1000402b-vnet
    - Subnet name: subnet1
    - Subnet: 10.204.0.0/24
    - Resource Group Name: az1000402b-RG


#### Task 2: Create a private DNS zone
  
1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the Azure subscription you intend to use in this lab.

1. In the Azure portal, navigate to the **Create a resource** blade.

1. From the **Create a resource** blade, search Azure Marketplace for **Private DNS zones**.

1. Select **Private DNS Zones**, and then click **Create**.

1. From the to **Create private DNS zone** blade, create a new DNS zone with the following settings: 

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: the name of a new resource group **az1000402b-RG**

    - Name: adatum.local

    - Resource group location: **East US** (or a supported region near you)

1. Once the private DNS zone is created, navigate to **Virtual Network Links** under **Settings** section

1. Click **Add** and create link to Vnet1 with below settings
    - Link name: vnet1Link
    - Vnet Network: az1000402b-vnet1
    - Enable Auto registration: Enabled

1. Click second link by **Add** and create link to Vnet2 with below settings
    - Link name: vnet2Link
    - Vnet Network: az1000402b-vnet2
    - Enable Auto registration: Disabled

#### Task 3: Deploy Azure VMs into virtual networks

1. Create new Azure VM with below configuration in First Virtual Network:
    - Name: az1000402b-vm1
    - Vnet: az1000402b-vnet1
    - SKU: Standard_DS2_v2
    - Subnet: subnet1
    - User name: **Student**
    - Password: **Pa55w.rd1234**

2. Create second Azure VM with below configuration in Second Virtual Network:
    - Name: az1000402b-vm2
    - Vnet: az1000402b-vnet2
    - SKU: Standard_DS2_v2
    - Subnet: subnet1
    - User name: **Student**
    - Password: **Pa55w.rd1234**

   > **Note**: Wait for both deployments to complete before you proceed to the next task.


#### Task 4: Validate Azure DNS-based name reservation and resolution for the private domain

1. In the Azure portal, navigate to the blade of the **az1000402b-vm2** Azure VM. 

1. From the **Overview** pane of the **az1000402b-vm2** blade, generate an RDP file and use it to connect to **az1000402b-vm2**.

1. When prompted, authenticate by specifying the following credentials:

    - User name: **Student**

    - Password: **Pa55w.rd1234**

1. Within the Remote Desktop session to **az1000402b-vm2**, start a Command Prompt window and run the following: 

   ```
   nslookup az1000402b-vm1.adatum.local
   ```

1. Verify that the name is successfully resolved.

1. Switch back to the lab virtual machine and, in the Azure portal window, create an additional DNS record in the private DNS zone **adatum.local** (record set):

    - Name: www
    - Record Type: A
    - IP address: 10.104.0.4
    - TTL : 1 hour

1. Switch again to the Remote Desktop session to **az1000402b-vm2** and run the following from the Command Prompt window: 

   ```
   nslookup www.adatum.local
   ```

1. Verify that the name is successfully resolved.

> **Result**: After completing this exercise, you have provisioned a multi-virtual network environment, created a private DNS zone, deployed Azure VMs into virtual networks, and validated Azure DNS-based name reservation and resolution for the private domain

## Exercise 3: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1000401b-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1000401b-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.

1. Repeat the steps for **az1000402b-RG** resource group

---
lab:
    title: 'Azure Network Watcher'
    module: 'Module 06 - Monitoring'
---

# Lab: Use Azure Network Watcher for monitoring and troubleshooting network connectivity

All tasks in this lab are performed from the Azure portal 


### Scenario
  
Adatum Corporation wants to monitor Azure virtual network connectivity by using Azure Network Watcher.


### Objectives
  
After completing this lab, you will be able to:

-  Deploy Azure VMs, Azure storage accounts, and Azure SQL Database instances by using Azure Resource Manager templates

-  Use Azure Network Watcher to monitor network connectivity


### Exercise 1: Prepare infrastructure for Azure Network Watcher-based monitoring
  
The main tasks for this exercise are as follows:

1. Deploy Azure VMs, an Azure Storage account, and an Azure SQL Database instance 

1. Enable Azure Network Watcher service

1. Establish peering between Azure virtual networks

1. Establish service endpoints to an Azure Storage account and Azure SQL Database instance


#### Task 1: Deploy Azure VMs, an Azure Storage account, and an Azure SQL Database instance

1. From the lab virtual machine, start Microsoft Edge, browse to the Azure portal at [**http://portal.azure.com**](http://portal.azure.com) and sign in by using a Microsoft account that has the Owner role in the target Azure subscription.

1. Create a new resource group with below configuration

   - Name: az1010301b-RG
   - Location: the name of the Azure region which is closest to the lab location and where you can provision Azure VMs and Azure SQL Database (Eg: East US)

1. In the Azure portal, create a Virtual Network with below configuration:

   - Virtual Network Name: az1010301b-vnet1
   - Subnet name: subnet0
   - Location: same as resource group location
   - Resource Group Name: az1010301b-RG
   - Vnet Address space: 10.203.0.0/20
   - Subnet Address space: 10.203.0.0/24

1. Create a Virtual Machine with below configuration

   - Vnet: az1010301b-vnet1
   - Subnet: subnet0
   - Location: same as resource group location
   - Resource Group: az1010301b-RG
   - Name: az1010301b-vm1
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Vm Size: Standard_DS2_v2
   - NIC name: az1010301b-nic1
   - publicIPAddressName: az1010301b-pip1

1. Create a new SQL database server and database instance with below configuration

   - Location: same as resource group location
   - Resource Group: az1010301b-RG
   - Sql Login Name: Student
   - Sql Login Password: Pa55w.rd1234
   - Database Name: az1010301b-db1
   - Sku Name: Basic
   - Sku Tier: Basic
   - SQL server name prefix: sql1010301b

1. Create a new storage account with below configuration

   - Location: same as resource group location
   - Resource Group: az1010301b-RG
   - storageAccountName prefix: az1010301b
   - storageAccountSku: Standard_LRS
   - storageAccountKind: Storage

   > **Note**: Do not wait for the deployment to complete but proceed to the next step. 

1. Deploy second set of resources starting with resource group with below configuration

   - Name: az1010302b-RG
   - Location: the name of an Azure region where you can provision Azure VMs, but which is different from the one you selected during previous deployment (Eg: West US)

1. In the Azure portal, create a second Virtual Network with below configuration:

   - Virtual Network Name: az1010302b-vnet2
   - Subnet name: subnet0
   - Location: same as resource group location
   - Resource Group Name: az1010302b-RG
   - Vnet Address space: 10.203.16.0/20
   - Subnet Address space: 10.203.16.0/24

1. Create a second Virtual Machine with below configuration

   - Vnet: az1010302b-vnet2
   - Subnet: subnet0
   - Location: same as resource group location
   - Resource Group: az1010302b-RG
   - Name: az1010302b-vm2
   - Admin Username: Student
   - Admin Password: Pa55w.rd1234
   - Vm Size: Standard_DS2_v2
   - NIC name: az1010302b-nic1
   - publicIPAddressName: az1010302b-pip1

   > **Note**: Make sure to choose a different Azure region for this deployment

   > **Note**: Do not wait for the deployment to complete but proceed to the next step. 


#### Task 2: Enable Azure Network Watcher service

1. In the Azure portal, use the search text box on the **All services** blade to navigate to the **Network Watcher** blade.

2. On the **Network Watcher** blade, verify that Network Watcher is enabled in both Azure regions into which you deployed resources in the previous task and, if not, enable it.


#### Task 3: Establish peering between Azure virtual networks

   > **Note**: Before you start this task, ensure that the deployment you started in the first task of this exercise has completed. 

1. In the Azure portal, navigate to the **az1010301b-vnet1** virtual network blade.

1. From the **az1010301b-vnet1** virtual network blade, display the **az1010301b-vnet1 - Peerings** blade.

1. From the **az1010301b-vnet1 - Peerings** blade, create a VNet peering with the following settings:

    - Name: **az1010301b-vnet1-to-az1010302b-vnet2**

    - Virtual network deployment model: **Resource manager**

    - Subscription: the name of the Azure subscription you are using in this lab

    - Virtual network: **az1010302b-vnet2**

    - Name of peering from az1010302b-vnet2 to az1010301b-vnet1: **az1010302b-vnet2-to-az1010301b-vnet1**

    - Allow virtual network access: **Enabled**

    - Allow forwarded traffic: **disabled**

    - Allow gateway transit: disabled

    > **Note**: The Azure portal allows you to configure both directions of the peering simultaneously. When using other management tools, each direction must be configured independently. 

#### Task 4: Establish service endpoints to an Azure Storage account and Azure SQL Database instance

1. In the Azure portal, navigate to the **az1010301b-vnet1** virtual network blade.

1. From the **az1010301b-vnet1** virtual network blade, display the **Service endpoints** blade.

1. From the **az1010301b-vnet1 - Service endpoints** blade, add a service endpoint with the following settings:

    - Service: **Microsoft.Storage**

    - Subnets: **subnet0**

1. Repeat the step to create a second service endpoint:

    - Service: **Microsoft.Sql**

    - Subnets: **subnet0**

1. In the Azure portal, navigate to the **az1010301b-RG** resource group blade.

1. From the **az1010301b-RG** resource group blade, navigate to the blade of the storage account included in the resource group. 

1. From the storage account blade, navigate to its **Firewalls and virtual networks** blade.

1. From the **Firewalls and virtual networks** blade of the storage account, configure the following settings:

    - Allow access from: **Selected networks**

    - Virtual networks: 

        - VIRTUAL NETWORK: **az1010301b-vnet1**

            - SUBNET: **subnet0**

    - Firewall: 

        - ADDRESS RANGE: none

    - Exceptions: 

        - Allow trusted Microsoft services to access this storage account: **Enabled**

        - Allow read access to storage logging from any network: **Disabled**

        - Allow read access to storage metrics from any network: **Disabled**

1. In the Azure portal, navigate to the **az1010301b-RG** resource group blade.

1. From the **az1010301b-RG** resource group blade, navigate to the **az1010301b** Azure SQL Server blade. 

1. From the Azure SQL Server blade, navigate to its server's **Firewalls and virtual networks** blade.

1. From the **Firewalls and virtual networks** blade of the Azure SQL Database server, configure the following settings:

    - Allow access to Azure services: **ON**

    - No firewall rules configured 

    - Virtual networks:

        - Name: **az1010301b-vnet1**

        - Subscription: the name of the subscription you are using in this lab

        - Virtual network: **az1010301b-vnet1**

        - Subnet name: **subnet0/ 10.203.0.0/24**

> **Result**: After you completed this exercise, you have deployed Azure VMs, an Azure Storage account, and an Azure SQL Database instance by using Azure Resource Manager templates, enabled Azure Network Watcher service, established global peering between Azure virtual networks, and established service endpoints to an Azure Storage account and Azure SQL Database instance.


### Exercise 2: Use Azure Network Watcher to monitor network connectivity
  
The main tasks for this exercise are as follows:

1. Test network connectivity to an Azure VM via virtual network peering by using Network Watcher

1. Test network connectivity to an Azure Storage account by using Network Watcher

1. Test network connectivity to an Azure SQL Database by using Network Watcher


#### Task 1: Test network connectivity to an Azure VM via virtual network peering by using Network Watcher

1. In the Azure portal, navigate to the **Network Watcher** blade.

1. From the **Network Watcher** blade, navigate to the **Connection troubleshoot**.

1. On the **Network Watcher - Connection troubleshoot** blade, initiate a check with the following settings:

    - Source: 

        - Subscription: the name of the Azure subscription you are using in this lab

        - Resource group: **az1010301b-RG**

        - Source type: **Virtual machine**

        - Virtual machine: **az1010301b-vm1**

    - Destination: **Specify manually**

        - URI, FQDN or IPv4: **10.203.16.4**

      > **Note**: **10.203.16.4** is the private IP address of the second Azure VM az1010302b-vm1 which you deployed to another Azure region

    - Probe Settings:

        - Protocol: **TCP**

        - Destination port: **3389**

    - Advanced settings:

        - Source port: blank

1. Wait until results of the connectivity check are returned and verify that the status is **Reachable**. Review the network path and note that the connection was direct, with no intermediate hops in between the VMs.

    > **Note**: If this is the first time you are using Network Watcher, the check can take up to 5 minutes.


#### Task 2: Test network connectivity to an Azure Storage account by using Network Watcher

1. From the Azure Portal, navigate to storage account and get the blob storage FQDN (Eg: mystore1.blob.core.windows.net)

1. Get the IP address of blob storage account by passing URL from previous step:

   ```sh
   nslookup <blob store FQDN>
   ```

1. Note the resulting string and, from the **Network Watcher - Connection troubleshoot** blade, initiate a check with the following settings:

    - Source: 

        - Subscription: the name of the Azure subscription you are using in this lab

        - Resource group: **az1010301b-RG**

        - Source type: **Virtual machine**

        - Virtual machine: **az1010301b-vm1**

    - Destination: **Specify manually**

        - URI, FQDN or IPv4: the IP address of the blob service endpoint of the storage account you identified in the previous step of this task

    - Probe Settings:

        - Protocol: **TCP**

        - Destination port: **443**

    - Advanced settings:

        - Source port: blank

1. Wait until results of the connectivity check are returned and verify that the status is **Reachable**. Review the network path and note that the connection was direct, with no intermediate hops in between the VMs, with minimal latency. 

    > **Note**: The connection takes place over the service endpoint you created in the previous exercise. To verify this, you will use the **Next hop** tool of Network Watcher.

1. From the **Network Watcher - Connection troubleshoot** blade, navigate to the **Network Watcher - Next hop** blade and test next hop with the following settings:

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: **az1010301b-RG**

    - Virtual machine: **az1010301b-vm1**

    - Network interface: **az1010301b-nic1**

    - Source IP address: **10.203.0.4**

    - Destination IP address: the IP address of the blob service endpoint of the storage account you identified earlier in this task

1. Verify that the result identifies the next hop type as **VirtualNetworkServiceEndpoint**

1. From the **Network Watcher - Connection troubleshoot** blade, initiate a check with the following settings:

    - Source: 

        - Subscription: the name of the Azure subscription you are using in this lab

        - Resource group: **az1010302b-RG**

        - Source type: **Virtual machine**

        - Virtual machine: **az1010302b-vm2**

    - Destination: **Specify manually**

        - URI, FQDN or IPv4: the IP address of the blob service endpoint of the storage account you identified earlier in this task

    - Probe Settings:

        - Protocol: **TCP**

        - Destination port: **443**

    - Advanced settings:

        - Source port: blank

1. Wait until results of the connectivity check are returned and verify that the status is **Reachable**. 

    > **Note**: The connection is successful, however it is established over Internet. To verify this, you will use again the **Next hop** tool of Network Watcher.

1. From the **Network Watcher - Connection troubleshoot** blade, navigate to the **Network Watcher - Next hop** blade and test next hop with the following settings:

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: **az1010302b-RG**

    - Virtual machine: **az1010302b-vm2**

    - Network interface: **az1010302b-nic1**

    - Source IP address: **10.203.16.4**

    - Destination IP address: the IP address of the blob service endpoint of the storage account you identified earlier in this task

1. Verify that the result identifies the next hop type as **Internet**


#### Task 3: Test network connectivity to an Azure SQL Database by using Network Watcher

1. From the Azure Portal, navigate to SQL Database server instance and get the FQDN of Azure SQL server (Eg: mystore1.database.windows.net)

1. Get the IP address of Azure Database Server by passing URL from previous step:

   ```sh
   nslookup <DB Server FQDN>
   ```

1. Note the resulting string and, from the **Network Watcher - Connection troubleshoot** blade, initiate a check with the following settings:

    - Source: 

        - Subscription: the name of the Azure subscription you are using in this lab

        - Resource group: **az1010301b-RG**

        - Source type: **Virtual machine**

        - Virtual machine: **az1010301b-vm1**

    - Destination: **Specify manually**

        - URI, FQDN or IPv4: the IP address of the Azure SQL Database server you identified in the previous step of this task

    - Probe Settings:

        - Protocol: **TCP**

        - Destination port: **1433**

    - Advanced settings:

        - Source port: blank

1. Wait until results of the connectivity check are returned and verify that the status is **Reachable**. Review the network path and note that the connection was direct, with no intermediate hops in between the VMs, with low latency. 

    > **Note**: The connection takes place over the service endpoint you created in the previous exercise. To verify this, you will use the **Next hop** tool of Network Watcher.

1. From the **Network Watcher - Connection troubleshoot** blade, navigate to the **Network Watcher - Next hop** blade and test next hop with the following settings:

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: **az1010301b-RG**

    - Virtual machine: **az1010301b-vm1**

    - Network interface: **az1010301b-nic1**

    - Source IP address: **10.203.0.4**

    - Destination IP address: the IP address of the Azure SQL Database server you identified earlier in this task

1. Verify that the result identifies the next hop type as **VirtualNetworkServiceEndpoint**

1. From the **Network Watcher - Connection troubleshoot** blade, initiate a check with the following settings:

    - Source: 

        - Subscription: the name of the Azure subscription you are using in this lab

        - Resource group: **az1010302b-RG**

        - Source type: **Virtual machine**

        - Virtual machine: **az1010302b-vm2**

    - Destination: **Specify manually**

        - URI, FQDN or IPv4: the IP address of the Azure SQL Database server you identified earlier in this task

    - Probe Settings:

        - Protocol: **TCP**

        - Destination port: **1433**

    - Advanced settings:

        - Source port: blank

1. Wait until results of the connectivity check are returned and verify that the status is **Reachable**. 

    > **Note**: The connection is successful, however it is established over Internet. To verify this, you will use again the **Next hop** tool of Network Watcher.

1. From the **Network Watcher - Connection troubleshoot** blade, navigate to the **Network Watcher - Next hop** blade and test next hop with the following settings:

    - Subscription: the name of the Azure subscription you are using in this lab

    - Resource group: **az1010302b-RG**

    - Virtual machine: **az1010302b-vm2**

    - Network interface: **az1010302b-nic1**

    - Source IP address: **10.203.16.4**

    - Destination IP address: the IP address of the Azure SQL Database server you identified earlier in this task

1. Verify that the result identifies the next hop type as **Internet**


> **Result**: After you completed this exercise, you have used Azure Network Watcher to test network connectivity to an Azure VM via virtual network peering, network connectivity to Azure Storage, and network connectivity to Azure SQL Database.

## Exercise 3: Remove lab resources

#### Task 1: Open Azure Portal

1. Open Azure portal, navigate to resource group **az1010301b-RG** and click on **Delete Resource Group** icon. Provide the name of resource group **az1010301b-RG** in the confirmation window and click on **Delete** icon to delete the resources created in this lab.

1. Repeat the steps for **az1010302b-RG** resource group
